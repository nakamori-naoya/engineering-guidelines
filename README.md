# エンジニアリング設計ガイドライン - AI向け参照ドキュメント

生成AIがデータベース設計で迷った時に参照するガイドライン集。

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

## 🤖 AI向け構造化データ

```yaml
guidelines:
  immutable-data-model:
    id: "8"
    title: "イミュータブルデータモデル設計ガイドライン"
    core-principle: "UPDATE（更新）がシステムを最も複雑化する"
    when-to-use:
      - "テーブルの更新可否を判断する時"
      - "データの変更履歴を管理する必要がある時"
      - "システムの複雑さを削減したい時"
    
  entity-classification:
    id: "9"
    title: "エンティティ分類の手引き"
    when-to-use:
      - "エンティティの種類（リソース/イベント）を判別する時"
      - "テーブル設計の基本方針を決める時"
    key-concepts:
      - "業務活動の日時属性を持つ = イベント"
      - "それ以外 = リソース"
    
  event-design:
    id: "10"
    title: "イベント設計の手引き"
    when-to-use:
      - "複数の日時属性を持つテーブルを設計する時"
      - "イベントの粒度を決定する時"
    principle: "1イベント = 1日時"
    
  resource-design:
    id: "11"
    title: "リソース設計の手引き"
    when-to-use:
      - "更新日時などの属性を追加したくなった時"
      - "リソースの変更を管理する必要がある時"
    concept: "隠れたイベントの抽出"
    
  generation-patterns:
    id: "12"
    title: "世代管理パターンの手引き"
    when-to-use:
      - "履歴データの管理方法を決める時"
      - "過去の状態を参照する必要がある時"
    patterns:
      - "シングル世代テーブルパターン"
      - "有効世代ビューパターン"
      - "世代バージョンタグ付けパターン"
    
  relationship-design:
    id: "13"
    title: "リレーションシップ設計の手引き"
    when-to-use:
      - "テーブル間の関係性を設計する時"
      - "NULL可能な外部キーが発生しそうな時"
    solution: "交差エンティティの活用"
    
  sql-antipatterns:
    id: "15"
    title: "SQLアンチパターン回避ガイドライン"
    author: "Bill Karwin"
    when-to-use:
      - "データベース設計のベストプラクティスを確認する時"
      - "一般的な設計ミスを避けたい時"
    
  logical-antipatterns:
    id: "16"
    title: "論理設計のアンチパターン"
    when-to-use:
      - "テーブルの論理設計を行う時"
      - "データの格納方法を決定する時"
    antipatterns:
      - "ジェイウォーク（カンマ区切りリスト）"
      - "ナイーブツリー（階層構造）"
      - "EAV（汎用属性テーブル）"
    
  physical-antipatterns:
    id: "17"
    title: "物理設計のアンチパターン"
    when-to-use:
      - "テーブルの物理構造を設計する時"
      - "カラムの定義を行う時"
    antipatterns:
      - "マルチカラムアトリビュート"
      - "メタデータトリブル"
    
  query-antipatterns:
    id: "18"
    title: "クエリのアンチパターン"
    when-to-use:
      - "SQLクエリを作成する時"
      - "パフォーマンス問題を解決する時"
    antipatterns:
      - "フィア・オブ・ジ・アンノウン（NULL恐怖症）"
      - "ランダムセレクション"
      - "プアマンズサーチエンジン"
    
  application-antipatterns:
    id: "19"
    title: "アプリケーション開発のアンチパターン"
    when-to-use:
      - "アプリケーション層でのDB操作を実装する時"
      - "セキュリティを考慮する必要がある時"
    antipatterns:
      - "SQLインジェクション"
      - "リーダブルパスワード"
      - "マジックビーンズ"
```

## 📋 Issue一覧

- [#8 イミュータブルデータモデル設計ガイドライン](https://github.com/nakamori-naoya/engineering-guidelines/issues/8) - UPDATE排除の設計原則
- [#9 エンティティ分類の手引き](https://github.com/nakamori-naoya/engineering-guidelines/issues/9) - リソースとイベントの判別
- [#10 イベント設計の手引き](https://github.com/nakamori-naoya/engineering-guidelines/issues/10) - 単一日時属性の原則
- [#11 リソース設計の手引き](https://github.com/nakamori-naoya/engineering-guidelines/issues/11) - 隠れたイベントの抽出
- [#12 世代管理パターンの手引き](https://github.com/nakamori-naoya/engineering-guidelines/issues/12) - 履歴管理の設計パターン
- [#13 リレーションシップ設計の手引き](https://github.com/nakamori-naoya/engineering-guidelines/issues/13) - 関係性の適切な表現
- [#14 設計ガイド評価レポート](https://github.com/nakamori-naoya/engineering-guidelines/issues/14) - ガイドラインの品質評価
- [#15 SQLアンチパターン回避ガイドライン](https://github.com/nakamori-naoya/engineering-guidelines/issues/15) - Bill Karwinの20のアンチパターン
- [#16 論理設計のアンチパターン](https://github.com/nakamori-naoya/engineering-guidelines/issues/16) - ジェイウォーク、ナイーブツリー、IDリクワイアド、キーレスエントリ、EAV、ポリモーフィック関連
- [#17 物理設計のアンチパターン](https://github.com/nakamori-naoya/engineering-guidelines/issues/17) - マルチカラムアトリビュート、メタデータトリブル
- [#18 クエリのアンチパターン](https://github.com/nakamori-naoya/engineering-guidelines/issues/18) - フィア・オブ・ジ・アンノウン、アンビギュアスグループ、ランダムセレクション、プアマンズサーチエンジン
- [#19 アプリケーション開発のアンチパターン](https://github.com/nakamori-naoya/engineering-guidelines/issues/19) - スパゲッティクエリ、インプリシットカラム、リーダブルパスワード、SQLインジェクション、シュードキー・ニートフリーク、シー・ノー・エビル、ディプロマティック・イミュニティ、マジックビーンズ

---

**注意**: このリポジトリはAIが設計判断で迷った時の参照用です。