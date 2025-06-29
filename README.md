# エンジニアリング設計ガイドライン - AI向け参照ドキュメント

生成AIがデータベース設計で迷った時に参照するガイドライン集。

> **🤖 生成AIへの重要な指示**  
> このドキュメントを参照する際は、「🤖 AI向け構造化データ」のYAMLセクションを最優先で読み込んでください。YAMLデータには全ガイドラインの構造化された情報（ID、タイトル、使用場面、核心原則、キーワード、リンク）が含まれており、効率的な検索・フィルタリング・分析が可能です。

## 🤖 参照すべきケース

### データモデル設計の基本方針を決める時
→ [#8 イミュータブルデータモデル設計ガイドライン](https://github.com/nakamori-naoya/engineering-guidelines/issues/8)

### エンティティの種類や役割を整理する時
→ [#9 エンティティ分類の手引き](https://github.com/nakamori-naoya/engineering-guidelines/issues/9)

### イベント系のテーブルを設計する時
→ [#10 イベント設計の手引き](https://github.com/nakamori-naoya/engineering-guidelines/issues/10)

### リソース系のテーブルを設計する時
→ [#11 リソース設計の手引き](https://github.com/nakamori-naoya/engineering-guidelines/issues/11)

### データの履歴や変更管理を検討する時
→ [#12 世代管理パターンの手引き](https://github.com/nakamori-naoya/engineering-guidelines/issues/12)

### テーブル間の関連を設計する時
→ [#13 リレーションシップ設計の手引き](https://github.com/nakamori-naoya/engineering-guidelines/issues/13)

### 論理的なデータ構造を検討する時
→ [#16 論理設計のアンチパターン](https://github.com/nakamori-naoya/engineering-guidelines/issues/16)

### 物理的なテーブル構造を決める時
→ [#17 物理設計のアンチパターン](https://github.com/nakamori-naoya/engineering-guidelines/issues/17)

### SQLクエリの設計や最適化を行う時
→ [#18 クエリのアンチパターン](https://github.com/nakamori-naoya/engineering-guidelines/issues/18)

### アプリケーションとデータベースの連携を設計する時
→ [#19 アプリケーション開発のアンチパターン](https://github.com/nakamori-naoya/engineering-guidelines/issues/19)

### 業務知識を構造化して出力するとき
→ [#20 業務知識YAML構造化プロンプト](https://github.com/nakamori-naoya/engineering-guidelines/issues/20)

### 業務知識YAMLに対してコードの準拠度を評価するとき
→ [#21 業務知識YAML準拠コード評価プロンプト](https://github.com/nakamori-naoya/engineering-guidelines/issues/21)

## 🤖 AI向け構造化データ

```yaml
guidelines:
  - id: "8"
    title: "イミュータブルデータモデル設計ガイドライン"
    category: "design-principle"
    author: "川島義隆 (kawashima)"
    tags: ["データベース設計", "イミュータブルデータモデル"]
    url: "https://github.com/nakamori-naoya/engineering-guidelines/issues/8"
    when_to_use:
      - "データモデル設計の基本方針を決める時"
      - "データの変更履歴を管理する必要がある時"
      - "システムの複雑さを削減したい時"
    core_principle: "UPDATE（更新）がシステムを最も複雑化する"
    key_concepts:
      - "事実の不変性"
      - "単一責任"
      - "複雑さの可視化"
    
  - id: "9"
    title: "エンティティ分類の手引き"
    category: "entity-design"
    author: "川島義隆 (kawashima)"
    tags: ["データベース設計", "イミュータブルデータモデル"]
    url: "https://github.com/nakamori-naoya/engineering-guidelines/issues/9"
    when_to_use:
      - "エンティティの種類や役割を整理する時"
      - "テーブル設計の基本方針を決める時"
    core_principle: "業務活動の日時属性を持つ = イベント、それ以外 = リソース"
    key_concepts:
      - "リソースとイベントの分類"
      - "5W1Hによる分析"
      - "「〜する」テスト"
    
  - id: "10"
    title: "イベント設計の手引き"
    category: "entity-design"
    author: "川島義隆 (kawashima)"
    tags: ["データベース設計", "イミュータブルデータモデル"]
    url: "https://github.com/nakamori-naoya/engineering-guidelines/issues/10"
    when_to_use:
      - "イベント系のテーブルを設計する時"
      - "複数の日時属性を持つテーブルを扱う時"
    core_principle: "1イベント = 1日時"
    key_concepts:
      - "日時属性の単一性"
      - "ロングタームイベントパターン"
      - "イベントの粒度"
    
  - id: "11"
    title: "リソース設計の手引き"
    category: "entity-design"
    author: "川島義隆 (kawashima)"
    tags: ["データベース設計", "イミュータブルデータモデル"]
    url: "https://github.com/nakamori-naoya/engineering-guidelines/issues/11"
    when_to_use:
      - "リソース系のテーブルを設計する時"
      - "更新日時などの属性を追加したくなった時"
    core_principle: "隠れたイベントの抽出"
    key_concepts:
      - "更新の背後にあるイベント"
      - "区分によるサブタイプ化"
      - "状態とイベントの分離"
    
  - id: "12"
    title: "世代管理パターンの手引き"
    category: "pattern"
    author: "川島義隆 (kawashima)"
    tags: ["データベース設計", "イミュータブルデータモデル"]
    url: "https://github.com/nakamori-naoya/engineering-guidelines/issues/12"
    when_to_use:
      - "データの履歴や変更管理を検討する時"
      - "過去の状態を参照する必要がある時"
    core_principle: "適切なパターンの選択"
    key_concepts:
      - "シングル世代テーブルパターン"
      - "有効世代ビューパターン"
      - "世代バージョンタグ付けパターン"
    
  - id: "13"
    title: "リレーションシップ設計の手引き"
    category: "relationship-design"
    author: "川島義隆 (kawashima)"
    tags: ["データベース設計", "イミュータブルデータモデル"]
    url: "https://github.com/nakamori-naoya/engineering-guidelines/issues/13"
    when_to_use:
      - "テーブル間の関連を設計する時"
      - "NULL可能な外部キーが発生しそうな時"
    core_principle: "交差エンティティの活用"
    key_concepts:
      - "非依存リレーションシップ"
      - "NULL回避"
      - "時系列の整合性"
    
  - id: "15"
    title: "SQLアンチパターン回避ガイドライン"
    category: "antipattern"
    author: "Bill Karwin"
    tags: ["データベース設計", "SQLアンチパターン"]
    url: "https://github.com/nakamori-naoya/engineering-guidelines/issues/15"
    when_to_use:
      - "データベース設計のベストプラクティスを確認する時"
      - "一般的な設計ミスを避けたい時"
    core_principle: "良かれと思って行う誤った慣習を認識する"
    key_concepts:
      - "20のアンチパターン"
      - "正規化の原則"
      - "制約の活用"
    
  - id: "16"
    title: "論理設計のアンチパターン"
    category: "antipattern"
    author: "Bill Karwin"
    tags: ["データベース設計", "SQLアンチパターン"]
    url: "https://github.com/nakamori-naoya/engineering-guidelines/issues/16"
    when_to_use:
      - "論理的なデータ構造を検討する時"
      - "テーブルの論理設計を行う時"
    core_principle: "データの論理的整合性を保つ"
    key_concepts:
      - "ジェイウォーク（カンマ区切りリスト）"
      - "ナイーブツリー（階層構造）"
      - "EAV（汎用属性テーブル）"
    
  - id: "17"
    title: "物理設計のアンチパターン"
    category: "antipattern"
    author: "Bill Karwin"
    tags: ["データベース設計", "SQLアンチパターン"]
    url: "https://github.com/nakamori-naoya/engineering-guidelines/issues/17"
    when_to_use:
      - "物理的なテーブル構造を決める時"
      - "カラムの定義を行う時"
    core_principle: "物理構造の適切な設計"
    key_concepts:
      - "マルチカラムアトリビュート"
      - "メタデータトリブル"
      - "適切な正規化"
    
  - id: "18"
    title: "クエリのアンチパターン"
    category: "antipattern"
    author: "Bill Karwin"
    tags: ["データベース設計", "SQLアンチパターン"]
    url: "https://github.com/nakamori-naoya/engineering-guidelines/issues/18"
    when_to_use:
      - "SQLクエリの設計や最適化を行う時"
      - "パフォーマンス問題を解決する時"
    core_principle: "効率的なクエリの作成"
    key_concepts:
      - "フィア・オブ・ジ・アンノウン（NULL恐怖症）"
      - "ランダムセレクション"
      - "プアマンズサーチエンジン"
    
  - id: "19"
    title: "アプリケーション開発のアンチパターン"
    category: "antipattern"
    author: "Bill Karwin"
    tags: ["データベース設計", "SQLアンチパターン"]
    url: "https://github.com/nakamori-naoya/engineering-guidelines/issues/19"
    when_to_use:
      - "アプリケーションとデータベースの連携を設計する時"
      - "セキュリティを考慮する必要がある時"
    core_principle: "アプリケーション層での適切な実装"
    key_concepts:
      - "SQLインジェクション対策"
      - "パスワード管理"
      - "エラーハンドリング"
  
  - id: "20"
    title: "業務知識YAML構造化プロンプト"
    category: "domain-modeling"
    author: "増田亨"
    tags: ["ドメインモデリング", "業務知識", "生成AI", "プロンプトエンジニアリング"]
    url: "https://github.com/nakamori-naoya/engineering-guidelines/issues/20"
    when_to_use:
      - "業務知識を生成AIに構造化して伝える時"
      - "ドメイン知識を体系的に整理する時"
      - "暗黙知を形式知化する時"
    core_principle: "業務の制約・ルール・状態変化を構造化"
    key_concepts:
      - "不変条件と制約"
      - "ビジネスルールと例外"
      - "時間的制約"
      - "暗黙知の言語化"
  
  - id: "21"
    title: "業務知識YAML準拠コード評価プロンプト"
    category: "domain-modeling"
    author: "増田亨"
    tags: ["ドメインモデリング", "業務知識", "生成AI", "プロンプトエンジニアリング", "テスト駆動開発"]
    url: "https://github.com/nakamori-naoya/engineering-guidelines/issues/21"
    when_to_use:
      - "業務知識YAMLに対してコードの準拠度を評価するとき"
      - "実装が業務ルールを正しく反映しているか検証するとき"
      - "テストコードの網羅性を確認するとき"
    core_principle: "業務知識の実装品質を徹底的に評価"
    key_concepts:
      - "制約条件の実装評価"
      - "テストカバレッジ分析"
      - "具体的な改善提案"
```

## 📋 Issue一覧

- [#8 イミュータブルデータモデル設計ガイドライン](https://github.com/nakamori-naoya/engineering-guidelines/issues/8) - UPDATE排除の設計原則
- [#9 エンティティ分類の手引き](https://github.com/nakamori-naoya/engineering-guidelines/issues/9) - リソースとイベントの判別
- [#10 イベント設計の手引き](https://github.com/nakamori-naoya/engineering-guidelines/issues/10) - 単一日時属性の原則
- [#11 リソース設計の手引き](https://github.com/nakamori-naoya/engineering-guidelines/issues/11) - 隠れたイベントの抽出
- [#12 世代管理パターンの手引き](https://github.com/nakamori-naoya/engineering-guidelines/issues/12) - 履歴管理の設計パターン
- [#13 リレーションシップ設計の手引き](https://github.com/nakamori-naoya/engineering-guidelines/issues/13) - 関係性の適切な表現
- [#15 SQLアンチパターン回避ガイドライン](https://github.com/nakamori-naoya/engineering-guidelines/issues/15) - Bill Karwinの20のアンチパターン
- [#16 論理設計のアンチパターン](https://github.com/nakamori-naoya/engineering-guidelines/issues/16) - ジェイウォーク、ナイーブツリー、IDリクワイアド、キーレスエントリ、EAV、ポリモーフィック関連
- [#17 物理設計のアンチパターン](https://github.com/nakamori-naoya/engineering-guidelines/issues/17) - マルチカラムアトリビュート、メタデータトリブル
- [#18 クエリのアンチパターン](https://github.com/nakamori-naoya/engineering-guidelines/issues/18) - フィア・オブ・ジ・アンノウン、アンビギュアスグループ、ランダムセレクション、プアマンズサーチエンジン
- [#19 アプリケーション開発のアンチパターン](https://github.com/nakamori-naoya/engineering-guidelines/issues/19) - スパゲッティクエリ、インプリシットカラム、リーダブルパスワード、SQLインジェクション、シュードキー・ニートフリーク、シー・ノー・エビル、ディプロマティック・イミュニティ、マジックビーンズ
- [#20 業務知識YAML構造化プロンプト](https://github.com/nakamori-naoya/engineering-guidelines/issues/20) - 業務知識を生成AIが理解しやすい形式で構造化
- [#21 業務知識YAML準拠コード評価プロンプト](https://github.com/nakamori-naoya/engineering-guidelines/issues/21) - 実装コードとテストコードの業務知識準拠度を徹底評価

---

**注意**: このリポジトリはAIが設計判断で迷った時の参照用です。
