# エンジニアリング設計ガイドライン インデックス

エンジニアリング設計に関するガイドラインとその詳細な手引きへのリンク集

## 🏗️ イミュータブルデータモデル設計

### 📘 メインガイドライン
- [CLAUDE2.md](./CLAUDE2.md) - イミュータブルデータモデル設計ガイドライン

### 📚 詳細な手引き
- [エンティティ分類の手引き](./CLAUDE2-guide-entity-classification.md) `#entity-classification` `#resource` `#event`
- [イベント設計の手引き](./CLAUDE2-guide-event-design.md) `#event-design` `#single-timestamp` `#long-term-event`
- [リソース設計の手引き](./CLAUDE2-guide-resource-design.md) `#resource-design` `#hidden-event` `#subtype`
- [世代管理パターンの手引き](./CLAUDE2-guide-generation-patterns.md) `#generation-pattern` `#version-control` `#history`
- [リレーションシップ設計の手引き](./CLAUDE2-guide-relationship-design.md) `#relationship` `#foreign-key` `#intersection-entity`

### 📊 評価レポート
- [設計ガイド評価レポート](./CLAUDE2-guide-evaluation.md) - ガイドラインの品質評価

## 🚫 SQLアンチパターン

### 📘 メインガイドライン
- [CLAUDE3.md](./CLAUDE3.md) - Bill Karwin著「SQL Antipatterns」に基づくガイドライン

### 📚 詳細な手引き
- [論理設計のアンチパターン](./CLAUDE3-guide-logical-antipatterns.md) `#logical-design` `#normalization` `#foreign-key`
- [物理設計のアンチパターン](./CLAUDE3-guide-physical-antipatterns.md) `#physical-design` `#table-design` `#partitioning`
- [クエリのアンチパターン](./CLAUDE3-guide-query-antipatterns.md) `#query` `#performance` `#sql-optimization`
- [アプリケーション開発のアンチパターン](./CLAUDE3-guide-application-antipatterns.md) `#application` `#security` `#error-handling`

## 🔍 タグによる検索

### イミュータブルデータモデル関連
- `#immutable` - イミュータブルデータモデル全般
- `#entity-classification` - エンティティ分類
- `#resource` - リソースエンティティ
- `#event` - イベントエンティティ
- `#generation-pattern` - 世代管理パターン
- `#single-timestamp` - 単一日時属性
- `#hidden-event` - 隠れたイベント
- `#relationship` - リレーションシップ設計

### SQLアンチパターン関連
- `#sql-antipatterns` - SQLアンチパターン全般
- `#logical-design` - 論理設計
- `#physical-design` - 物理設計
- `#query` - クエリ最適化
- `#application` - アプリケーション開発
- `#normalization` - 正規化
- `#foreign-key` - 外部キー
- `#security` - セキュリティ
- `#performance` - パフォーマンス

## 📖 使い方

### AIエージェントとして参照する場合
1. 特定のトピックについて質問された場合、関連するタグで検索
2. メインガイドライン（CLAUDE2.md, CLAUDE3.md）で概要を確認
3. 詳細が必要な場合は、対応する手引きファイルを参照

### 設計で迷った場合
1. 問題のカテゴリを特定（データモデル or SQL）
2. 関連する手引きファイルを確認
3. トラブルシューティングセクションを参照

### 例：「イベントとリソースの違いがわからない」
→ [エンティティ分類の手引き](./CLAUDE2-guide-entity-classification.md) を参照

### 例：「JOINが多すぎて困っている」
→ [論理設計のアンチパターン](./CLAUDE3-guide-logical-antipatterns.md) の「ナイーブツリー」セクションを確認

## 🎯 クイックリファレンス

### イミュータブルデータモデルの核心
- **UPDATE（更新）がシステムを最も複雑化する**
- 事実の不変性・単一責任・複雑さの可視化

### SQLアンチパターンの基本原則
- **良かれと思って行う誤った慣習を認識する**
- 正規化の原則を尊重・制約の活用・シンプルさの追求

---

本インデックスは、エンジニアリング設計の品質向上と、AIエージェントの効率的な情報アクセスを目的として作成されました。