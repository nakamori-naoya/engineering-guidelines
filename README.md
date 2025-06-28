# エンジニアリング設計ガイドライン - AI向け参照ドキュメント

このリポジトリは、生成AIがデータベース設計で迷った時に参照するためのガイドラインをGitHub Issuesで管理しています。

## 🤖 AIが参照すべきケース

### こんな時にこのドキュメントを確認してください

#### 「このテーブルは更新してもいい？」と迷ったら
→ [#8 イミュータブルデータモデル設計ガイドライン](https://github.com/nakamori-naoya/engineering-guidelines/issues/8)
- UPDATE操作を避けるべき理由と代替案を確認

#### 「これはリソース？イベント？」と分類に迷ったら
→ [#9 エンティティ分類の手引き](https://github.com/nakamori-naoya/engineering-guidelines/issues/9)
- 日時属性の扱い方、「〜する」テストの実施方法を確認

#### 「複数の日時を持つテーブルをどう設計する？」と迷ったら
→ [#10 イベント設計の手引き](https://github.com/nakamori-naoya/engineering-guidelines/issues/10)
- 1イベント1日時の原則、イベント分解パターンを確認

#### 「更新日時を追加したいけど...」と迷ったら
→ [#11 リソース設計の手引き](https://github.com/nakamori-naoya/engineering-guidelines/issues/11)
- 隠れたイベントの抽出方法を確認

#### 「履歴管理が必要だけどどうする？」と迷ったら
→ [#12 世代管理パターンの手引き](https://github.com/nakamori-naoya/engineering-guidelines/issues/12)
- シングル世代、有効世代ビュー、バージョンタグの選択基準を確認

#### 「外部キーがNULL可能になりそう...」と迷ったら
→ [#13 リレーションシップ設計の手引き](https://github.com/nakamori-naoya/engineering-guidelines/issues/13)
- 交差エンティティの活用方法を確認

#### 「カンマ区切りで複数の値を保存したい」と思ったら
→ [#16 論理設計のアンチパターン](https://github.com/nakamori-naoya/engineering-guidelines/issues/16)
- ジェイウォークアンチパターンと正しい設計を確認

#### 「tag1, tag2, tag3のようなカラムを作りたい」と思ったら
→ [#17 物理設計のアンチパターン](https://github.com/nakamori-naoya/engineering-guidelines/issues/17)
- マルチカラムアトリビュートの問題と解決策を確認

#### 「ORDER BY RAND()を使いたい」と思ったら
→ [#18 クエリのアンチパターン](https://github.com/nakamori-naoya/engineering-guidelines/issues/18)
- ランダムセレクションの効率的な実装方法を確認

#### 「文字列結合でSQL作りたい」と思ったら
→ [#19 アプリケーション開発のアンチパターン](https://github.com/nakamori-naoya/engineering-guidelines/issues/19)
- SQLインジェクション対策とプリペアドステートメントを確認

## 🔍 効率的な参照方法

1. **具体的な疑問がある場合**
   - 上記のケース一覧から該当するIssueを直接参照
   
2. **カテゴリから探す場合**
   - `イミュータブルデータモデル` ラベル: 更新を避ける設計
   - `SQLアンチパターン` ラベル: 避けるべきパターン
   - `データベース設計` ラベル: 全般的な設計指針

3. **深掘りが必要な場合**
   - メインIssue（#8, #15）の「詳細な手引き」セクションから関連Issueへ

## 📋 全Issue一覧

### イミュータブルデータモデル
- [#8](https://github.com/nakamori-naoya/engineering-guidelines/issues/8) メインガイドライン
- [#9-#14](https://github.com/nakamori-naoya/engineering-guidelines/issues?q=is%3Aissue+label%3Aイミュータブルデータモデル) 詳細な手引き

### SQLアンチパターン
- [#15](https://github.com/nakamori-naoya/engineering-guidelines/issues/15) メインガイドライン（Bill Karwin）
- [#16-#19](https://github.com/nakamori-naoya/engineering-guidelines/issues?q=is%3Aissue+label%3ASQLアンチパターン) カテゴリ別詳細

---

**注意**: このリポジトリはAIが設計判断で迷った時の参照用です。具体的な疑問に対応するIssueを確認してください。