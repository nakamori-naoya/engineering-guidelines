# 物理設計アンチパターンの手引き

Bill Karwin著「SQL Antipatterns」の物理設計に関するアンチパターンを実践的に解説する。

## 🤔 AIが理解に困る典型的な状況

### 1. マルチカラムアトリビュート - 繰り返し項目

#### 見分け方
```sql
-- アンチパターンのサイン
users (
  user_id INT PRIMARY KEY,
  phone1 VARCHAR(20),
  phone2 VARCHAR(20),
  phone3 VARCHAR(20),
  email1 VARCHAR(255),
  email2 VARCHAR(255),
  email3 VARCHAR(255)
)

-- または
survey_responses (
  response_id INT PRIMARY KEY,
  question1_answer VARCHAR(255),
  question2_answer VARCHAR(255),
  question3_answer VARCHAR(255),
  -- ... question20_answer まで
)
```

#### なぜ問題か
- カラム数の上限が固定
- NULL値の無駄
- クエリの複雑化（OR条件の羅列）
- 新規項目追加時のスキーマ変更

#### 解決パターン

**パターン1: 1対多の関係**
```sql
-- ユーザー
users (
  user_id INT PRIMARY KEY,
  name VARCHAR(255)
)

-- 電話番号（1対多）
user_phones (
  phone_id INT PRIMARY KEY,
  user_id INT,
  phone_number VARCHAR(20),
  phone_type VARCHAR(20),  -- 'mobile', 'home', 'work'
  is_primary BOOLEAN DEFAULT FALSE,
  FOREIGN KEY (user_id) REFERENCES users(user_id)
)

-- メールアドレス（1対多）
user_emails (
  email_id INT PRIMARY KEY,
  user_id INT,
  email VARCHAR(255),
  email_type VARCHAR(20),  -- 'personal', 'work'
  is_primary BOOLEAN DEFAULT FALSE,
  FOREIGN KEY (user_id) REFERENCES users(user_id)
)
```

**パターン2: 動的な項目の場合**
```sql
-- アンケート回答
survey_responses (
  response_id INT PRIMARY KEY,
  survey_id INT,
  respondent_id INT
)

-- 各質問への回答
question_answers (
  answer_id INT PRIMARY KEY,
  response_id INT,
  question_id INT,
  answer_text TEXT,
  FOREIGN KEY (response_id) REFERENCES survey_responses(response_id),
  FOREIGN KEY (question_id) REFERENCES questions(question_id)
)
```

#### 判断基準
- 項目数が可変 → 別テーブル
- 項目ごとに属性がある → 別テーブル
- すべての項目を検索対象にする → 別テーブル

### 2. メタデータトリブル（メタデータ大増殖）

#### 見分け方
```sql
-- アンチパターンのサイン
sales_2022_01 (...)
sales_2022_02 (...)
sales_2022_03 (...)
-- 毎月新しいテーブル

-- または
customers_tokyo (...)
customers_osaka (...)
customers_nagoya (...)
-- 地域ごとのテーブル
```

#### なぜ問題か
- 複数テーブルをまたぐクエリが複雑
- テーブル作成の自動化が必要
- メタデータの管理が煩雑
- 統計情報の分散

#### 解決パターン

**パターン1: 単一テーブル + パーティショニング**
```sql
-- 論理的には1つのテーブル
sales (
  sale_id INT,
  sale_date DATE,
  amount DECIMAL(10,2),
  region VARCHAR(50),
  PRIMARY KEY (sale_id, sale_date)
) PARTITION BY RANGE (YEAR(sale_date), MONTH(sale_date)) (
  PARTITION p202201 VALUES LESS THAN (2022, 2),
  PARTITION p202202 VALUES LESS THAN (2022, 3),
  -- ...
);
```

**パターン2: 単一テーブル + インデックス**
```sql
-- シンプルな単一テーブル
sales (
  sale_id INT PRIMARY KEY,
  sale_date DATE,
  amount DECIMAL(10,2),
  region VARCHAR(50),
  INDEX idx_date (sale_date),
  INDEX idx_region (region)
)
```

**パターン3: アーカイブ戦略**
```sql
-- 現行データ
sales_current (
  sale_id INT PRIMARY KEY,
  sale_date DATE,
  -- 直近1年分
)

-- アーカイブデータ
sales_archive (
  sale_id INT PRIMARY KEY,
  sale_date DATE,
  -- 1年以上前のデータ
)

-- ビューで統合
CREATE VIEW sales_all AS
SELECT * FROM sales_current
UNION ALL
SELECT * FROM sales_archive;
```

#### 選択基準

**パーティショニングが適している場合：**
- データ量が膨大（数千万行以上）
- 期間や地域での分割が明確
- 古いデータの定期削除が必要
- DBMSがパーティショニングをサポート

**単一テーブルが適している場合：**
- データ量が中規模以下
- 全期間のデータを頻繁に参照
- シンプルさを重視

**アーカイブが適している場合：**
- アクセス頻度に明確な差がある
- 古いデータは参照のみ
- パフォーマンス要件が異なる

## 💡 設計のコツ

### 繰り返し項目の識別
1. カラム名に数字が含まれる
2. 同じプレフィックスのカラムが複数
3. ALTER TABLEでカラム追加が予想される

### テーブル分割の判断
1. テーブル名に日付や地域が含まれる
2. 定期的なCREATE TABLEが必要
3. UNION ALLを多用するクエリ

### 正しい物理設計の原則
- **論理的な一体性を保つ**
- **物理的な最適化は透過的に**
- **メンテナンスの自動化を考慮**

## ⚠️ よくある間違い

### 1. 安易なカラム追加
```sql
-- ❌ 要件が増えるたびに
ALTER TABLE users ADD COLUMN phone4 VARCHAR(20);
ALTER TABLE users ADD COLUMN phone5 VARCHAR(20);

-- ✅ 最初から拡張性を考慮
-- 別テーブルなら無限に追加可能
```

### 2. パフォーマンスを理由にしたテーブル分割
```sql
-- ❌ 「速くなるはず」という思い込み
CREATE TABLE orders_2024_tokyo (...);
CREATE TABLE orders_2024_osaka (...);

-- ✅ 実測に基づく最適化
-- パーティショニングやインデックスを先に検討
```

### 3. アプリケーションでの分岐
```php
// ❌ テーブル名の動的生成
$table = "sales_" . date('Y_m');
$sql = "INSERT INTO $table ...";

// ✅ 単一テーブルへの挿入
$sql = "INSERT INTO sales (sale_date, ...) VALUES (NOW(), ...)";
```

## 🔍 チェックリスト

物理設計で確認すること：

- [ ] 連番カラムを作っていないか
- [ ] 同一構造のテーブルを複製していないか
- [ ] カラム数の上限を設けていないか
- [ ] テーブル名に日付や地域を含めていないか
- [ ] パーティショニングを検討したか
- [ ] アーカイブ戦略を検討したか

## 📝 実装例

### 効率的な連絡先管理
```sql
-- ユーザーの連絡先を柔軟に管理
CREATE TABLE contact_methods (
  contact_id INT PRIMARY KEY AUTO_INCREMENT,
  user_id INT NOT NULL,
  contact_type ENUM('phone', 'email', 'sns'),
  contact_value VARCHAR(255) NOT NULL,
  label VARCHAR(50),  -- 'work', 'home', 'mobile'等
  is_primary BOOLEAN DEFAULT FALSE,
  is_verified BOOLEAN DEFAULT FALSE,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  INDEX idx_user (user_id),
  INDEX idx_type (contact_type),
  FOREIGN KEY (user_id) REFERENCES users(user_id)
);

-- 主要連絡先の取得
SELECT contact_value 
FROM contact_methods 
WHERE user_id = ? AND is_primary = TRUE;

-- すべての電話番号の取得
SELECT contact_value, label 
FROM contact_methods 
WHERE user_id = ? AND contact_type = 'phone';
```