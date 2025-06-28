# エンジニアリング設計ガイドライン

このリポジトリは、エンジニアリング設計に関するガイドラインをGitHub Issuesで管理しています。

## 📋 ガイドラインの確認方法

すべてのガイドラインは[Issues](https://github.com/nakamori-naoya/engineering-guidelines/issues)に整理されています。

### 🔍 用途別の検索方法

#### 1. 設計手法を学びたい場合
- ラベル: `イミュータブルデータモデル` を検索
- [#8 イミュータブルデータモデル設計ガイドライン](https://github.com/nakamori-naoya/engineering-guidelines/issues/8) から開始

#### 2. アンチパターンを避けたい場合
- ラベル: `SQLアンチパターン` を検索
- [#15 SQLアンチパターン回避ガイドライン](https://github.com/nakamori-naoya/engineering-guidelines/issues/15) から開始

#### 3. データベース設計全般
- ラベル: `データベース設計` を検索
- すべての設計ガイドラインが表示されます

## 🎯 深掘りの方法

各メインガイドラインには、詳細な手引きへのリンクが含まれています：

1. **メインIssueを確認** - 概要と基本原則を理解
2. **「詳細な手引き」セクション** - 深掘りが必要な場合は、リンクされているサブイシューを必ず確認
3. **具体例とチェックリスト** - 各手引きには実装例とチェックリストが含まれています

## 📊 ガイドライン一覧

### イミュータブルデータモデル設計
- [#8](https://github.com/nakamori-naoya/engineering-guidelines/issues/8) メインガイドライン
- [#9](https://github.com/nakamori-naoya/engineering-guidelines/issues/9) エンティティ分類の手引き
- [#10](https://github.com/nakamori-naoya/engineering-guidelines/issues/10) イベント設計の手引き
- [#11](https://github.com/nakamori-naoya/engineering-guidelines/issues/11) リソース設計の手引き
- [#12](https://github.com/nakamori-naoya/engineering-guidelines/issues/12) 世代管理パターンの手引き
- [#13](https://github.com/nakamori-naoya/engineering-guidelines/issues/13) リレーションシップ設計の手引き
- [#14](https://github.com/nakamori-naoya/engineering-guidelines/issues/14) 設計ガイド評価レポート

### SQLアンチパターン回避
- [#15](https://github.com/nakamori-naoya/engineering-guidelines/issues/15) メインガイドライン（Bill Karwin）
- [#16](https://github.com/nakamori-naoya/engineering-guidelines/issues/16) 論理設計のアンチパターン
- [#17](https://github.com/nakamori-naoya/engineering-guidelines/issues/17) 物理設計のアンチパターン
- [#18](https://github.com/nakamori-naoya/engineering-guidelines/issues/18) クエリのアンチパターン
- [#19](https://github.com/nakamori-naoya/engineering-guidelines/issues/19) アプリケーション開発のアンチパターン

## 💡 活用シーン

1. **新規設計時** - ガイドラインに従って設計を行う
2. **コードレビュー時** - チェックリストとして使用
3. **リファクタリング時** - 改善ポイントの発見
4. **学習時** - 体系的な知識の習得

## 🏷️ ラベル一覧

- `データベース設計` - データベース設計全般
- `イミュータブルデータモデル` - UPDATE排除の設計手法
- `SQLアンチパターン` - 避けるべき設計パターン

---

**注意**: このリポジトリにはファイルは含まれていません。すべての内容はIssuesで管理されています。
EOF < /dev/null