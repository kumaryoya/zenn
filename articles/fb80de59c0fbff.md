---
title: "【Tips】ユーザーマニュアルもコードレビューの対象に！Rails + Markdown でサービス内に組み込んだ話"
emoji: "📒"
type: "tech"
topics: [ai, markdown, rails, gem, tips]
published: true
publication_name: "linkedge"
---

## はじめに

弊社ではこれまで、自社サービスのユーザーマニュアルを Google Sites で管理・公開していました。

しかし、以下のような課題がありました。

- **バージョン管理やコードレビューができない**
  - Google Sites は Git などのバージョン管理システムと連携できないため、変更履歴の確認やプルリクエストを通じたレビューができませんでした。
- **ユーザー体験を損なう**
  - ユーザーマニュアルを確認する際にサービス外へ遷移してしまう、サービス本体とデザインが異なるなど、ユーザー体験を損なう要因がありました。
- **運用面での不便さ**
  - Google アカウントでの権限管理が必要だったり、機能リリースとユーザーマニュアル更新のタイミングがずれてしまうなど、運用上の課題がありました。

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

今回は、Ruby on Rails で開発されている自社サービスに組み込む形でユーザーマニュアルを管理するため、Markdown を HTML に変換する `redcarpet` という gem を導入しました。

Gemfile に追加し、`bundle install` を実行します。

```diff ruby:Gemfile
+ gem 'redcarpet'
```

```bash
docker compose exec app bundle install
```

https://rubygems.org/gems/redcarpet

https://github.com/vmg/redcarpet

### ヘルパーの作成

次に、Markdown を HTML に変換するヘルパーメソッドを作成します。

設定項目については、`redcarpet` の README を参考に適宜調整してください。

```diff ruby:app/helpers/application_helper.rb
module ApplicationHelper
+ def render_markdown(file_path)
+   markdown_content = File.read(file_path)
+   renderer = Redcarpet::Render::HTML.new
+   markdown = Redcarpet::Markdown.new(renderer, fenced_code_blocks: true, autolink: true)
+   markdown.render(markdown_content).html_safe
+ end
end
```

これで、Markdown を HTML に変換する準備が整いました。

### ユーザーマニュアルの作成

実際にテスト用のユーザーマニュアルを作成してみましょう。

まず、ルーティング、コントローラー、ビューを作成します。

```diff ruby:config/routes.rb
Rails.application.routes.draw do
+ resources :user_manuals, only: [] do
+   collection do
+     get :test
+   end
+ end
end
```

```diff ruby:app/controllers/user_manuals_controller.rb
+ class UserManualsController < ApplicationController
+   def test; end
+ end
```

```diff erb:app/views/user_manuals/test.html.erb
+ <div class="p-5">
+   <%= render_markdown(Rails.root.join('docs', 'test.md')) %>
+ </div>
```

次に、`docs` ディレクトリにユーザーマニュアル用の Markdown ファイルを作成します。

```markdown:docs/test.md
# テスト

---

# 見出し

## 見出し

### 見出し

#### 見出し

##### 見出し

---

- リスト
  - リスト
- リスト
  - リスト

---

1. リスト
2. リスト

---

[リンク](https://example.com)

---

**太字**

---

*斜体*

---

<font color="red">文字色</font>
```

### 動作確認

`http://localhost:3000/user_manuals/test` にアクセスし、Markdown が正しくレンダリングされていることを確認します。

以下のように表示されれば成功です。

![](https://storage.googleapis.com/zenn-user-upload/f693217c6ba4-20251219.png =1000x)

## まとめ

今回は、Rails アプリケーション内で Markdown を HTML に変換し、ユーザーマニュアルを表示する方法を紹介しました。

この方法には、以下のようなメリットがあります。

- **バージョン管理とコードレビューの実現**
  - ユーザーマニュアルがコードベースに含まれるため、Git によるバージョン管理や PR を通じたレビューが可能になります。
- **一貫したユーザー体験の提供**
  - ユーザーマニュアルがサービス内で表示されるため、デザインの統一感が保たれ、ユーザー体験が向上します。
- **運用の効率化**
  - 機能リリースとユーザーマニュアル更新を同時にデプロイできるため、運用が効率化されます。

また、副産物として、AI にコードを書いてもらう際にユーザーマニュアルを参照させたり、ユーザーマニュアル自体を AI に生成してもらうことも可能になりました。

ユーザーマニュアルの量が多いと移行は大変かもしれませんが、得られるメリットは大きいので、ぜひ試してみてください。

最後までお読みいただき、ありがとうございました。
