---
title: "【Tips】ユーザーマニュアルもコードレビューの対象に！Google Sites から Rails + Markdown へ移行した話"
emoji: "📒"
type: "tech"
topics: [ai, markdown, rails, gem, tips]
published: false
publication_name: "linkedge"
---

## はじめに

弊社ではこれまで、自社サービスのユーザーマニュアルを Google Sites で管理・公開していました。

しかし、以下のような課題がありました。

- **バージョン管理やコードレビューができない**
  - Google Sites は Git などのバージョン管理システムと連携できないため、変更履歴の確認やプルリクエストを通じたレビューができませんでした。
- **ユーザー体験を損なう**
  - マニュアルを確認する際にサービス外へ遷移してしまう、サービス本体とデザインが異なるなど、ユーザー体験を損なう要因がありました。
- **運用面での不便さ**
  - Google アカウントでの権限管理が必要だったり、機能リリースとマニュアル更新のタイミングがずれてしまうなど、運用上の課題がありました。

これらの課題を解決するために、ユーザーマニュアルをサービスのリポジトリ内で Markdown ファイルとして管理する方法へ移行しました。

本記事では、移行の方法と得られたメリットについて紹介します。

![](https://storage.googleapis.com/zenn-user-upload/0b24eccf4d82-20251219.jpg =300x)

## 注意点

:::message alert
- 内容に誤りが含まれている可能性があります
- コメントなどでご指摘いただけると幸いです
:::

## 環境

:::message
- Docker Desktop：4.55.0
- Docker Engine：29.1.3
- Ruby：3.3.5
- Rails：7.0.8.6
:::

## 移行の方法

### gem の導入

今回は、Ruby on Rails で開発されている自社サービスに組み込む形でマニュアルを管理するため、Markdown を HTML に変換する `redcarpet` という gem を導入しました。

Gemfile に追加し、`bundle install` を実行します。

```diff ruby:Gemfile
+ gem 'redcarpet'
```

```bash
docker compose exec app bundle install
```

https://rubygems.org/gems/redcarpet

https://github.com/vmg/redcarpet
