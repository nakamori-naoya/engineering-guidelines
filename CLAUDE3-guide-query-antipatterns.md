# クエリアンチパターンの手引き

Bill Karwin著「SQL Antipatterns」のクエリに関するアンチパターンを実践的に解説する。

## 🤔 AIが理解に困る典型的な状況

### 1. フィア・オブ・ジ・アンノウン（NULLの恐怖）

#### 見分け方
```sql
-- アンチパターンのサイン
-- 空文字列をNULLの代わりに使用
INSERT INTO users (name, middle_name) VALUES ('John', '');

-- 0をNULLの代わりに使用
UPDATE products SET discount = 0 WHERE discount IS NULL;

-- -1を「未設定」の意味で使用
INSERT INTO orders (customer_id, referrer_id) VALUES (123, -1);
```

#### なぜ問題か
- 本来の意味が曖昧（0%割引？割引なし？）
- 集計関数での扱いが異なる
- 検索条件が複雑化
- ストレージの無駄

#### NULL比較の正しい方法
```sql
-- ❌ 間違い
SELECT * FROM users WHERE middle_name = NULL;
SELECT * FROM users WHERE middle_name != NULL;

-- ✅ 正しい
SELECT * FROM users WHERE middle_name IS NULL;
SELECT * FROM users WHERE middle_name IS NOT NULL;

-- ❌ 間違い（NULLは含まれない）
SELECT * FROM products WHERE price NOT IN (10, 20, 30);

-- ✅ 正しい（NULLも考慮）
SELECT * FROM products 
WHERE price NOT IN (10, 20, 30) OR price IS NULL;
```

#### NULL安全な比較
```sql
-- 三値論理を理解する
-- NULL = NULL → NULL (不明)
-- NULL != NULL → NULL (不明)

-- NULL安全等価演算子（MySQL）
SELECT * FROM users WHERE middle_name <=> NULL;

-- COALESCE/NVLを使った比較
SELECT * FROM users 
WHERE COALESCE(middle_name, '') = COALESCE(?, '');
```

### 2. アンビギュアスグループ（曖昧なグループ）

#### 見分け方
```sql
-- アンチパターンのサイン（MySQL特有）
SELECT customer_id, name, MAX(order_total)
FROM orders
GROUP BY customer_id;
-- nameはどの行から？
```

#### なぜ問題か
- 非決定的な結果
- 他のDBMSでエラー
- 意図が不明確
- バグの温床

#### 解決パターン

**パターン1: 完全なGROUP BY**
```sql
-- すべての非集約カラムをGROUP BYに含める
SELECT customer_id, customer_name, SUM(order_total)
FROM orders
GROUP BY customer_id, customer_name;
```

**パターン2: サブクエリ**
```sql
-- 最大値を持つ行を取得
SELECT o1.*
FROM orders o1
WHERE order_total = (
  SELECT MAX(o2.order_total)
  FROM orders o2
  WHERE o2.customer_id = o1.customer_id
);
```

**パターン3: ウィンドウ関数**
```sql
-- 各顧客の最大注文を取得
WITH ranked_orders AS (
  SELECT *,
    ROW_NUMBER() OVER (
      PARTITION BY customer_id 
      ORDER BY order_total DESC
    ) AS rn
  FROM orders
)
SELECT * FROM ranked_orders WHERE rn = 1;
```

### 3. ランダムセレクション

#### 見分け方
```sql
-- アンチパターンのサイン
SELECT * FROM products
ORDER BY RAND()
LIMIT 1;

-- または
SELECT * FROM users
ORDER BY RANDOM()  -- PostgreSQL
LIMIT 10;
```

#### なぜ問題か
- 全行の読み込みとソートが必要
- O(n log n)の計算量
- 大規模テーブルで致命的
- インデックス無効

#### 解決パターン

**パターン1: 事前計算**
```sql
-- ランダム値カラムを追加
ALTER TABLE products ADD COLUMN random_value FLOAT;
UPDATE products SET random_value = RAND();
CREATE INDEX idx_random ON products(random_value);

-- 高速なランダム選択
SELECT * FROM products
WHERE random_value >= RAND()
ORDER BY random_value
LIMIT 1;
```

**パターン2: 主キーベース（隙間がない場合）**
```sql
-- 最大IDを取得
SET @max_id = (SELECT MAX(product_id) FROM products);
SET @random_id = FLOOR(RAND() * @max_id) + 1;

-- ランダムな1行を取得
SELECT * FROM products
WHERE product_id >= @random_id
ORDER BY product_id
LIMIT 1;
```

**パターン3: サンプリング（近似でOKな場合）**
```sql
-- PostgreSQL: TABLESAMPLE
SELECT * FROM products
TABLESAMPLE SYSTEM (1)  -- 1%をサンプリング
ORDER BY RANDOM()
LIMIT 10;

-- MySQL: WHERE句でフィルタ
SELECT * FROM products
WHERE RAND() < 0.01  -- 約1%を選択
ORDER BY RAND()
LIMIT 10;
```

### 4. プアマンズサーチエンジン（貧者のサーチエンジン）

#### 見分け方
```sql
-- アンチパターンのサイン
SELECT * FROM articles
WHERE title LIKE '%keyword%'
   OR content LIKE '%keyword%'
   OR tags LIKE '%keyword%';

-- ワイルドカードの乱用
SELECT * FROM products
WHERE description LIKE '%red%shoes%';
```

#### なぜ問題か
- 先頭ワイルドカードでインデックス無効
- 複数カラムのOR条件で性能劣化
- 部分一致の精度問題
- 言語処理機能なし

#### 解決パターン

**パターン1: 全文検索インデックス**
```sql
-- MySQL: FULLTEXT
CREATE FULLTEXT INDEX ft_articles 
ON articles(title, content, tags);

SELECT * FROM articles
WHERE MATCH(title, content, tags) 
AGAINST('keyword' IN NATURAL LANGUAGE MODE);

-- PostgreSQL: GIN/GiST
CREATE INDEX idx_search ON articles 
USING gin(to_tsvector('english', title || ' ' || content));

SELECT * FROM articles
WHERE to_tsvector('english', title || ' ' || content) 
  @@ to_tsquery('english', 'keyword');
```

**パターン2: 検索用の正規化テーブル**
```sql
-- 検索キーワードテーブル
CREATE TABLE article_keywords (
  article_id INT,
  keyword VARCHAR(50),
  relevance FLOAT,
  PRIMARY KEY (article_id, keyword),
  INDEX idx_keyword (keyword)
);

-- 高速な検索
SELECT DISTINCT a.*
FROM articles a
JOIN article_keywords ak ON a.article_id = ak.article_id
WHERE ak.keyword IN ('red', 'shoes');
```

**パターン3: 専用検索エンジン**
```
-- Elasticsearch/Solrなどの利用
-- アプリケーション層での統合
```

## 💡 クエリ最適化のコツ

### NULL処理の原則
1. NULLは「不明」を表す
2. 三値論理を理解する
3. IS NULL/IS NOT NULLを使用
4. COALESCE/IFNULLで初期値設定

### GROUP BY の原則
1. 選択リストとGROUP BY句の一致
2. 集約関数の適切な使用
3. HAVING句とWHERE句の使い分け

### ランダム選択の原則
1. データ量を考慮
2. 完全なランダム性の必要性を検討
3. キャッシュの活用

### 検索機能の原則
1. 検索要件の明確化
2. 適切なインデックス選択
3. 専用ツールの検討

## ⚠️ よくある間違い

### 1. NULLの誤解
```sql
-- ❌ NULLは0ではない
SELECT COUNT(middle_name) FROM users;  -- NULLは除外
SELECT COUNT(*) FROM users;             -- 全行カウント

-- ❌ NULLの連鎖
SELECT NULL + 1;  -- 結果: NULL
SELECT CONCAT('Hello', NULL);  -- 結果: NULL
```

### 2. GROUP BYの誤用
```sql
-- ❌ 集約関数なしのDISTINCT代わり
SELECT customer_id FROM orders GROUP BY customer_id;

-- ✅ 素直にDISTINCT
SELECT DISTINCT customer_id FROM orders;
```

### 3. パフォーマンス無視
```sql
-- ❌ 常に全件ソート
SELECT * FROM huge_table ORDER BY created_at;

-- ✅ 必要な分だけ
SELECT * FROM huge_table ORDER BY created_at LIMIT 100;
```

## 🔍 チェックリスト

クエリ作成時の確認事項：

- [ ] NULLを適切に扱っているか
- [ ] GROUP BYは正しく使用されているか
- [ ] ランダム選択は効率的か
- [ ] 検索機能は適切に実装されているか
- [ ] インデックスは有効活用されているか
- [ ] 実行計画を確認したか