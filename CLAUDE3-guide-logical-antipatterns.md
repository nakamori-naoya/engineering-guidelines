# 論理設計アンチパターンの手引き

Bill Karwin著「SQL Antipatterns」の論理設計に関するアンチパターンを実践的に解説する。

## 🤔 AIが理解に困る典型的な状況

### 1. ジェイウォーク（信号無視）- カンマ区切りリスト

#### 見分け方
```sql
-- アンチパターンのサイン
products (
  product_id INT,
  category_ids VARCHAR(255)  -- '1,2,5,7'
)
```

#### なぜ問題か
- WHERE句でLIKE '%,2,%'のような検索が必要
- インデックスが効かない
- 参照整合性を保証できない
- カテゴリの追加・削除が複雑

#### 解決パターン
```sql
-- 交差テーブルパターン
product_categories (
  product_id INT,
  category_id INT,
  PRIMARY KEY (product_id, category_id),
  FOREIGN KEY (product_id) REFERENCES products(product_id),
  FOREIGN KEY (category_id) REFERENCES categories(category_id)
)
```

#### 判断基準
- データが「複数の値」を持つ → 別テーブル
- 検索・更新が必要 → 正規化
- 参照整合性が必要 → 外部キー

### 2. ナイーブツリー（素朴な木）- 階層構造

#### 見分け方
```sql
-- アンチパターンのサイン
categories (
  category_id INT PRIMARY KEY,
  parent_id INT,  -- 親カテゴリのID
  name VARCHAR(255)
)
```

#### なぜ問題か
- 子孫の取得に再帰クエリが必要
- 深さの制限や性能問題
- サブツリーの移動が困難

#### 解決パターン

**パターン1: 経路列挙**
```sql
categories (
  category_id INT PRIMARY KEY,
  path VARCHAR(255),  -- '1/3/7/10'
  name VARCHAR(255)
)
```

**パターン2: 入れ子集合**
```sql
categories (
  category_id INT PRIMARY KEY,
  left_value INT,
  right_value INT,
  name VARCHAR(255)
)
```

**パターン3: 閉包テーブル**
```sql
category_paths (
  ancestor_id INT,
  descendant_id INT,
  path_length INT,
  PRIMARY KEY (ancestor_id, descendant_id)
)
```

#### 選択基準
- 頻繁な参照、稀な更新 → 入れ子集合
- サブツリー操作が多い → 閉包テーブル
- シンプルさ重視 → 経路列挙

### 3. ID リクワイアド（とりあえずID）

#### 見分け方
```sql
-- アンチパターンのサイン
order_items (
  id INT PRIMARY KEY,  -- 不要なID
  order_id INT,
  product_id INT,
  -- order_id + product_idで一意なのに...
)
```

#### なぜ問題か
- 自然な複合キーを無視
- 重複データの可能性
- 余分なインデックス

#### 正しい判断基準
```sql
-- 複合主キーが適切な場合
order_items (
  order_id INT,
  product_id INT,
  quantity INT,
  PRIMARY KEY (order_id, product_id)
)

-- 代理キーが適切な場合
customers (
  customer_id INT PRIMARY KEY,  -- 自然キーが不安定
  email VARCHAR(255) UNIQUE,    -- 変更される可能性
  name VARCHAR(255)
)
```

### 4. キーレスエントリ（外部キー嫌い）

#### 見分け方
```sql
-- アンチパターンのサイン
orders (
  order_id INT PRIMARY KEY,
  customer_id INT  -- 外部キー制約なし
)
```

#### なぜ問題か
- 孤立レコード（オーファン）の発生
- アプリケーション側での整合性チェック負荷
- データ不整合のリスク

#### 解決策
```sql
orders (
  order_id INT PRIMARY KEY,
  customer_id INT NOT NULL,
  FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
    ON UPDATE CASCADE
    ON DELETE RESTRICT
)
```

#### カスケードオプションの選択
- `CASCADE` - 親の変更に追従
- `RESTRICT` - 子がある場合は拒否
- `SET NULL` - NULL許可の場合のみ
- `NO ACTION` - RESTRICT相当（DBMS依存）

### 5. EAV（エンティティ・アトリビュート・バリュー）

#### 見分け方
```sql
-- アンチパターンのサイン
product_attributes (
  product_id INT,
  attribute_name VARCHAR(255),
  attribute_value TEXT  -- 型が不明
)
```

#### なぜ問題か
- データ型の喪失
- 制約の適用不可
- クエリの複雑化
- インデックスの効果減

#### 解決パターン

**パターン1: 具体的なカラム**
```sql
products (
  product_id INT PRIMARY KEY,
  name VARCHAR(255),
  price DECIMAL(10,2),
  weight DECIMAL(8,3),
  color VARCHAR(50)
)
```

**パターン2: サブタイプ（継承）**
```sql
-- 共通属性
products (
  product_id INT PRIMARY KEY,
  name VARCHAR(255),
  price DECIMAL(10,2)
)

-- 書籍固有
books (
  product_id INT PRIMARY KEY,
  isbn VARCHAR(13),
  author VARCHAR(255),
  FOREIGN KEY (product_id) REFERENCES products(product_id)
)

-- 衣類固有
clothing (
  product_id INT PRIMARY KEY,
  size VARCHAR(10),
  color VARCHAR(50),
  FOREIGN KEY (product_id) REFERENCES products(product_id)
)
```

### 6. ポリモーフィック関連

#### 見分け方
```sql
-- アンチパターンのサイン
comments (
  comment_id INT PRIMARY KEY,
  entity_type VARCHAR(50),  -- 'product' or 'article'
  entity_id INT,           -- 対象のID
  comment_text TEXT
)
```

#### なぜ問題か
- 外部キー制約を設定できない
- 参照整合性の保証不可
- JOINが複雑

#### 解決パターン

**パターン1: 個別テーブル**
```sql
product_comments (
  comment_id INT PRIMARY KEY,
  product_id INT,
  comment_text TEXT,
  FOREIGN KEY (product_id) REFERENCES products(product_id)
)

article_comments (
  comment_id INT PRIMARY KEY,
  article_id INT,
  comment_text TEXT,
  FOREIGN KEY (article_id) REFERENCES articles(article_id)
)
```

**パターン2: 共通基底テーブル**
```sql
-- すべてのコメント可能エンティティ
commentables (
  commentable_id INT PRIMARY KEY,
  commentable_type VARCHAR(50)
)

-- 各エンティティが参照
products (
  product_id INT PRIMARY KEY,
  commentable_id INT UNIQUE,
  FOREIGN KEY (commentable_id) REFERENCES commentables(commentable_id)
)

-- コメントは基底を参照
comments (
  comment_id INT PRIMARY KEY,
  commentable_id INT,
  comment_text TEXT,
  FOREIGN KEY (commentable_id) REFERENCES commentables(commentable_id)
)
```

## 💡 判断のコツ

### アンチパターンの兆候
1. **文字列操作でのデータ処理**
2. **アプリケーション側での整合性チェック**
3. **動的SQL生成の必要性**
4. **型安全性の欠如**

### 設計時の確認事項
- [ ] 正規化は適切か
- [ ] 外部キー制約を設定できるか
- [ ] インデックスは効果的か
- [ ] 型安全性は保たれるか
- [ ] クエリはシンプルか

## 🔍 チェックリスト

論理設計で確認すること：

- [ ] カンマ区切りリストを使っていないか
- [ ] 階層構造の表現方法は適切か
- [ ] 不要な代理キーを追加していないか
- [ ] 外部キー制約を省略していないか
- [ ] EAVパターンに陥っていないか
- [ ] ポリモーフィック関連を使っていないか