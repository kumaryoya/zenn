---
name: update-claude-md
description: リポジトリの現在の状態を調査し、CLAUDE.md の各セクションを最新の情報に更新する。CLAUDE.md の整備、更新、メンテナンスを依頼されたときに使用する。
---

リポジトリの現在の状態を調査し、CLAUDE.md を最新の情報に更新する。

このリポジトリは Zenn コンテンツ用（`zenn-cli` 管理）。アプリコード・テスト・ビルドは無く、調査対象は記事構成と zenn-cli 周りに限られる。

## 調査対象

以下を並列で調査する:

1. **リポジトリ直下構成**: `ls` でトップレベルのファイル/ディレクトリ一覧（`articles/`, `books/`, `package.json` 等）
2. **zenn-cli バージョン**: `package.json` の `dependencies.zenn-cli`
3. **記事数と最近の追加**: `ls articles/ | wc -l` と直近のファイル（`ls -t articles/ | head -5` など）
4. **books/ の状況**: `ls books/`（`.keep` のみか、実体があるか）
5. **記事 frontmatter の運用実態**: `articles/` から数本サンプルし、`type`（tech/idea）、`publication_name` の有無、`published` の運用、`topics` の付け方の傾向を把握
6. **直近の動き**: `git log --oneline -10` でコミット傾向（リリース/編集の頻度感）

## 更新手順

### 1. 現在の CLAUDE.md を読む

まず `CLAUDE.md` の全文を読み、各セクションの内容を把握する。

### 2. 差分の特定

調査結果と CLAUDE.md の各セクションを比較し、以下の差分を洗い出す:

- **リポジトリの目的**: zenn-cli 管理であることや「アプリコードは無い」記述に齟齬がないか
- **よく使うコマンド**: `npx zenn` 系コマンドが現状の zenn-cli バージョンで有効か。新しいサブコマンドが追加されていないか
- **記事の構造**: frontmatter のキー（`title` / `emoji` / `type` / `topics` / `published` / `publication_name` 等）が実態と一致するか。実例で使われているがドキュメントに無いキーがあれば追記
- **books/ セクション**: `books/` の中身が増えていれば「`.keep` のみ」の記述を更新

### 3. CLAUDE.md の更新

差分がある場合のみ、該当セクションを Edit ツールで更新する。

更新時のルール:
- 既存のセクション構造（見出し階層・順序）を維持する
- 簡潔な記述を維持する。`zenn-cli` の公式ドキュメントを丸写ししない（必要なら「[zenn-cli ガイド](https://zenn.dev/zenn/articles/zenn-cli-guide) を参照」とリンクで済ませる）
- 差分がないセクションは一切変更しない
- frontmatter のサンプルを更新する場合は、実在する記事の値を参考にする（架空のキーを入れない）
- 文体・トーンは既存の CLAUDE.md（です・ます調と体言止めの混在、日本語）に揃える

### 4. 結果の報告

更新した内容を差分の要約として報告する。差分がなかった場合は「CLAUDE.md は最新の状態です」と報告する。
