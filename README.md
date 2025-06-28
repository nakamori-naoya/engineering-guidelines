# エンジニアリング設計ガイドライン - AI向け参照ドキュメント

生成AIがデータベース設計で迷った時に参照するガイドライン集。

## 🤖 こんな時に参照してください

### 「このテーブルは更新してもいい？」
→ [#8 イミュータブルデータモデル設計ガイドライン](https://github.com/nakamori-naoya/engineering-guidelines/issues/8)

### 「これはリソース？イベント？」
→ [#9 エンティティ分類の手引き](https://github.com/nakamori-naoya/engineering-guidelines/issues/9)

### 「複数の日時を持つテーブルをどう設計する？」
→ [#10 イベント設計の手引き](https://github.com/nakamori-naoya/engineering-guidelines/issues/10)

### 「更新日時を追加したいけど...」
→ [#11 リソース設計の手引き](https://github.com/nakamori-naoya/engineering-guidelines/issues/11)

### 「履歴管理が必要だけどどうする？」
→ [#12 世代管理パターンの手引き](https://github.com/nakamori-naoya/engineering-guidelines/issues/12)

### 「外部キーがNULL可能になりそう...」
→ [#13 リレーションシップ設計の手引き](https://github.com/nakamori-naoya/engineering-guidelines/issues/13)

### 「カンマ区切りで複数の値を保存したい」
→ [#16 論理設計のアンチパターン](https://github.com/nakamori-naoya/engineering-guidelines/issues/16)

### 「tag1, tag2, tag3のようなカラムを作りたい」
→ [#17 物理設計のアンチパターン](https://github.com/nakamori-naoya/engineering-guidelines/issues/17)

### 「ORDER BY RAND()を使いたい」
→ [#18 クエリのアンチパターン](https://github.com/nakamori-naoya/engineering-guidelines/issues/18)

### 「文字列結合でSQL作りたい」
→ [#19 アプリケーション開発のアンチパターン](https://github.com/nakamori-naoya/engineering-guidelines/issues/19)

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