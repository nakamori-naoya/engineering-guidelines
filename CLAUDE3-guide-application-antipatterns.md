# アプリケーション開発アンチパターンの手引き

Bill Karwin著「SQL Antipatterns」のアプリケーション開発に関するアンチパターンを実践的に解説する。

## 🤔 AIが理解に困る典型的な状況

### 1. スパゲッティクエリ

#### 見分け方
```sql
-- アンチパターンのサイン
SELECT 
  c.customer_id,
  c.name,
  o.order_id,
  o.order_date,
  SUM(oi.quantity * p.price) as order_total,
  COUNT(DISTINCT p.category_id) as category_count,
  GROUP_CONCAT(DISTINCT p.name) as product_names,
  (SELECT COUNT(*) FROM reviews r WHERE r.customer_id = c.customer_id) as review_count,
  (SELECT AVG(rating) FROM reviews r2 WHERE r2.customer_id = c.customer_id) as avg_rating,
  CASE 
    WHEN SUM(oi.quantity * p.price) > 1000 THEN 'VIP'
    WHEN SUM(oi.quantity * p.price) > 500 THEN 'Regular'
    ELSE 'New'
  END as customer_level
FROM customers c
LEFT JOIN orders o ON c.customer_id = o.customer_id
LEFT JOIN order_items oi ON o.order_id = oi.order_id
LEFT JOIN products p ON oi.product_id = p.product_id
WHERE o.order_date >= DATE_SUB(NOW(), INTERVAL 1 YEAR)
GROUP BY c.customer_id, o.order_id
HAVING order_total > 100
ORDER BY order_total DESC
LIMIT 100;
```

#### なぜ問題か
- 可読性が極めて低い
- デバッグが困難
- パフォーマンスチューニングが複雑
- 変更時の影響範囲が不明確

#### 解決パターン

**パターン1: クエリの分割**
```sql
-- ステップ1: 基本的な注文情報
CREATE TEMPORARY TABLE temp_orders AS
SELECT 
  o.order_id,
  o.customer_id,
  o.order_date,
  SUM(oi.quantity * p.price) as order_total
FROM orders o
JOIN order_items oi ON o.order_id = oi.order_id
JOIN products p ON oi.product_id = p.product_id
WHERE o.order_date >= DATE_SUB(NOW(), INTERVAL 1 YEAR)
GROUP BY o.order_id;

-- ステップ2: 顧客情報と結合
SELECT 
  c.customer_id,
  c.name,
  t.order_total,
  CASE 
    WHEN t.order_total > 1000 THEN 'VIP'
    WHEN t.order_total > 500 THEN 'Regular'
    ELSE 'New'
  END as customer_level
FROM customers c
JOIN temp_orders t ON c.customer_id = t.customer_id
WHERE t.order_total > 100
ORDER BY t.order_total DESC
LIMIT 100;
```

**パターン2: 共通テーブル式（CTE）**
```sql
WITH order_totals AS (
  SELECT 
    o.order_id,
    o.customer_id,
    SUM(oi.quantity * p.price) as order_total
  FROM orders o
  JOIN order_items oi ON o.order_id = oi.order_id
  JOIN products p ON oi.product_id = p.product_id
  WHERE o.order_date >= DATE_SUB(NOW(), INTERVAL 1 YEAR)
  GROUP BY o.order_id
),
customer_stats AS (
  SELECT 
    customer_id,
    COUNT(*) as review_count,
    AVG(rating) as avg_rating
  FROM reviews
  GROUP BY customer_id
)
SELECT 
  c.customer_id,
  c.name,
  ot.order_total,
  cs.review_count,
  cs.avg_rating
FROM customers c
JOIN order_totals ot ON c.customer_id = ot.customer_id
LEFT JOIN customer_stats cs ON c.customer_id = cs.customer_id
WHERE ot.order_total > 100
ORDER BY ot.order_total DESC
LIMIT 100;
```

### 2. インプリシットカラム（暗黙の列）

#### 見分け方
```sql
-- アンチパターンのサイン
SELECT * FROM users;
INSERT INTO products VALUES (1, 'Widget', 9.99);  -- カラム指定なし
```

#### なぜ問題か
- スキーマ変更時の予期しない動作
- 不要なデータ転送
- カラム順序依存のバグ
- コードの可読性低下

#### 解決パターン
```sql
-- カラムを明示的に指定
SELECT 
  user_id,
  username,
  email,
  created_at
FROM users;

-- INSERT時もカラム指定
INSERT INTO products (product_id, name, price) 
VALUES (1, 'Widget', 9.99);
```

### 3. リーダブルパスワード（読み取り可能パスワード）

#### 見分け方
```sql
-- アンチパターンのサイン
CREATE TABLE users (
  user_id INT PRIMARY KEY,
  username VARCHAR(50),
  password VARCHAR(50)  -- 平文
);

-- または弱い暗号化
UPDATE users SET password = MD5('mypassword');
```

#### なぜ問題か
- データ漏洩時の被害が甚大
- 内部犯行のリスク
- コンプライアンス違反
- 信頼性の喪失

#### 解決パターン
```sql
-- パスワードハッシュ用のカラム
CREATE TABLE users (
  user_id INT PRIMARY KEY,
  username VARCHAR(50) UNIQUE,
  password_hash VARCHAR(255),  -- bcrypt/scrypt/argon2
  salt VARCHAR(32)
);

-- アプリケーション側での実装例（PHP）
$hash = password_hash($password, PASSWORD_BCRYPT);
// 検証
if (password_verify($password, $hash)) {
    // 認証成功
}
```

### 4. SQLインジェクション

#### 見分け方
```php
// アンチパターンのサイン
$sql = "SELECT * FROM users WHERE username = '" . $_POST['username'] . "'";
$sql = "SELECT * FROM products WHERE price < " . $_GET['max_price'];
```

#### なぜ問題か
- データベースの完全な制御を奪われる
- データの窃取・改ざん・削除
- サーバーへの侵入
- 法的責任

#### 解決パターン

**パターン1: プリペアドステートメント**
```php
// PHP PDO
$stmt = $pdo->prepare("SELECT * FROM users WHERE username = ?");
$stmt->execute([$username]);

// 複数パラメータ
$stmt = $pdo->prepare(
    "SELECT * FROM products WHERE category = ? AND price < ?"
);
$stmt->execute([$category, $maxPrice]);
```

**パターン2: ストアドプロシージャ**
```sql
DELIMITER //
CREATE PROCEDURE GetUserByUsername(IN p_username VARCHAR(50))
BEGIN
    SELECT * FROM users WHERE username = p_username;
END //
DELIMITER ;
```

### 5. シュードキー・ニートフリーク（擬似キー潔癖症）

#### 見分け方
```sql
-- アンチパターンのサイン
-- 削除後にIDを振り直す
DELETE FROM products WHERE product_id = 5;
UPDATE products SET product_id = product_id - 1 WHERE product_id > 5;

-- 欠番を埋める
INSERT INTO orders (order_id, ...) 
VALUES ((SELECT MIN(t1.order_id + 1) 
         FROM orders t1 
         LEFT JOIN orders t2 ON t1.order_id + 1 = t2.order_id 
         WHERE t2.order_id IS NULL), ...);
```

#### なぜ問題か
- 外部キー参照の破壊
- 同時実行性の問題
- パフォーマンスの低下
- 監査証跡の喪失

#### 正しい理解
- 代理キーは意味を持たない
- 欠番は正常
- 連番である必要はない
- 表示用の番号は別途生成

### 6. シー・ノー・エビル（臭いものに蓋）

#### 見分け方
```php
// アンチパターンのサイン
try {
    $result = $db->query($sql);
} catch (Exception $e) {
    // 何もしない
}

// またはエラーを隠す
@mysql_query($sql);  // エラー抑制演算子
```

#### なぜ問題か
- バグの発見が遅れる
- データ不整合の見逃し
- デバッグが困難
- 本番環境での予期しない動作

#### 解決パターン
```php
// 適切なエラーハンドリング
try {
    $result = $db->query($sql);
} catch (PDOException $e) {
    // エラーログに記録
    error_log("Database error: " . $e->getMessage());
    
    // ユーザーには一般的なメッセージ
    throw new UserException("データの取得に失敗しました");
}

// エラー情報の記録
function logDatabaseError($query, $error, $params = []) {
    $log = [
        'timestamp' => date('Y-m-d H:i:s'),
        'query' => $query,
        'error' => $error,
        'params' => $params,
        'backtrace' => debug_backtrace(DEBUG_BACKTRACE_IGNORE_ARGS)
    ];
    error_log(json_encode($log));
}
```

## 💡 設計のコツ

### セキュリティファースト
1. 入力は常に信頼しない
2. 最小権限の原則
3. 多層防御
4. 定期的な監査

### エラーハンドリング
1. エラーは隠さない
2. 適切なログレベル
3. ユーザーへの情報開示は最小限
4. 監視・アラートの設定

### コードの品質
1. 可読性を重視
2. 適切な抽象化
3. テスタビリティ
4. 保守性

## ⚠️ よくある間違い

### 1. パフォーマンスを理由にしたセキュリティ無視
```php
// ❌ 「プリペアドステートメントは遅い」という誤解
$sql = "SELECT * FROM users WHERE id = " . $id;

// ✅ セキュリティは必須要件
$stmt = $pdo->prepare("SELECT * FROM users WHERE id = ?");
```

### 2. 開発環境と本番環境の差異
```php
// ❌ 開発環境でのみエラー表示
if (ENVIRONMENT === 'development') {
    echo $error->getMessage();
}

// ✅ 適切なエラーハンドリング
error_log($error->getMessage());
if (ENVIRONMENT === 'development') {
    // 開発者向け詳細情報
}
```

## 🔍 チェックリスト

アプリケーション開発時の確認事項：

- [ ] SQLインジェクション対策をしているか
- [ ] パスワードは適切にハッシュ化されているか
- [ ] エラーハンドリングは適切か
- [ ] SELECT *を使っていないか
- [ ] 必要なカラムのみ選択しているか
- [ ] 適切な権限設定がされているか
- [ ] ログは適切に記録されているか
- [ ] 代理キーの性質を理解しているか