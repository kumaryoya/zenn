# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## リポジトリの目的

[Zenn](https://zenn.dev/) のコンテンツリポジトリで、[`zenn-cli`](https://zenn.dev/zenn/articles/zenn-cli-guide) を使って管理されている。著者が書いた Markdown 記事（および任意で本）が格納され、Zenn 側に同期される。アプリケーションコードは存在せず、「ビルド」成果物は `zenn-cli` がローカルで提供するプレビューのみ。

## よく使うコマンド

```bash
npx zenn preview           # ローカルプレビューサーバ（既定: http://localhost:8000）
npx zenn new:article       # ランダムスラッグで articles/ に新規記事を作成
npx zenn new:book          # books/ に新規の本を作成
npx zenn list:articles     # 記事一覧（スラッグ・公開状態を含む）
npx zenn list:books
```

テスト、リンタ、ビルド工程は存在しない。`package.json` の `npm test` はプレースホルダ。

## 記事の構造

各記事は `articles/<slug>.md` に置かれ、Zenn が読み取る frontmatter から始まる:

```yaml
---
title: "..."
emoji: "🍟"            # 記事アイコンとして表示される絵文字 1 つ
type: "tech"           # "tech" または "idea"
topics: [rails, ruby]  # タグ（スペースなし。実例では大文字始まりや日本語タグも使用）
published: true        # false にすると下書き扱い
publication_name: "linkedge"  # このリポジトリの全記事で "linkedge" を指定（Zenn Publication 配下）
---
```

新規記事を作るときはスラッグを手書きせず `npx zenn new:article` を使う。Zenn はファイル名として 14 文字の英小文字＋数字を要求する。執筆中は `published: false` のままにし、`true` に切り替えた時点で次回同期で公開される。

## 補足

- `books/` は存在するが現状 `.keep` のみ。本を追加する場合は各本ごとにサブディレクトリを切り、`config.yaml` と章ごとの Markdown を配置する形になる。
- 記事は日本語で書かれている。編集・追記する際は、特に指示がない限り既存のトーンや見出しスタイル（`## はじめに` 等）に揃える。
