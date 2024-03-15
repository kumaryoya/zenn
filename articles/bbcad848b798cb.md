---
title: "【Rails】Brakemanでセキュリティチェックをする"
emoji: "🔒"
type: "tech"
topics: [Rails, Ruby, gem, githubactions, circleci]
published: false
publication_name: "linkedge"
---

## はじめに

お疲れ様です！
おおくまです！

今回は、「【Rails】Brakeman でセキュリティチェックをする」ということで、**gem**の**Brakeman**についてまとめてみました！

少しでも皆様の参考になりますと幸いです！

![](https://storage.googleapis.com/zenn-user-upload/5a3b245e76fb-20240316.png =400x)

## 対象読者

:::message

- **Ruby on Rails**を学習中の方
- **Brakeman**について学びたい方
  :::

## 注意点

:::message alert
内容に誤りがある場合があります！
コメント等で教えていただけると幸甚です！
:::

## 環境

:::message

- Ruby：3.2.2
- Rails：7.0.6
  :::

## Brakeman とは？

[Brakeman](https://brakemanscanner.org/)は**Ruby on Rails**で書かれているアプリケーションの**セキュリティ脆弱性**を**静的解析**するための**gem**です！
**Brakeman**を実行することで、アプリケーションのコードをスキャンし、セキュリティ上の問題がある可能性のある箇所を特定し、警告してくれる、とても便利なツールです！
ターミナルで実行し、都度チェックすることも出来ますが、**CircleCI**に組み込むことで、チェックし忘れることがなくなるので、非常に便利です！

**Brakeman**は、様々な観点から**セキュリティ脆弱性**を解析してくれます！
**Brakeman**がどんな種類の警告をしてくれるかは以下のサイトをご確認ください！
https://brakemanscanner.org/docs/warning_types/

## Unscoped Find

**Brakeman**が警告してくれるものの１つに[Unscoped Find](https://brakemanscanner.org/docs/warning_types/unscoped_find/)というものがあります！
別のモデルに属するモデルには、通常、スコープ指定されたクエリを介してアクセスする必要があります！
例えば、`User`モデルと`Post`モデルで次のような関係があるとします！

```ruby:app/models/user.rb
has_many :posts, dependent: :destroy
```

```ruby:app/models/post.rb
belongs_to :user
```

次のように`Post`モデルにアクセスしている場合、安全ではない検索方法の可能性があります。

```ruby:app/controllers/posts_controller.rb
Post.find(params[:id])
```

`show`アクションなら大丈夫な場合もあるかもしれませんが、`edit`アクションや、`update`アクションでは、他人の`Post`レコードにアクセスできてしまいます。
それを防ぐために、現在ログインしているユーザーというスコープを設定する必要があります。

```ruby:app/controllers/posts_controller.rb
current_user.posts.find(params[:id])
```

このようなスコープの設定し忘れをチェックしてくれるのが、**Brakeman**の**Unscoped Find**という項目です！

https://brakemanscanner.org/docs/warning_types/unscoped_find/

## CircleCI で Unscoped Find を検出する

### インストール

```ruby:Gemfile
group :development do
  gem 'brakeman'
end
```

```ruby:ターミナル
bundle install
```

まずは、**Brakeman**をインストールします。

### コマンド例

#### ヘルプ

```ruby:ターミナル
bundle exec brakeman --help
```

#### 全てのチェックを実行

```ruby:ターミナル
bundle exec brakeman -A
```

#### 警告をスキップする項目を設定するファイルを作成する

```ruby:ターミナル
bundle exec brakeman -I

-Aと組み合わせる
bundle exec brakeman -IA
```

#### 特定のファイルのチェックをスキップする

```ruby:ターミナル
bundle exec brakeman --skip-files app/controllers/posts_controller.rb
```

#### 特定のチェック項目のみをチェックする

```ruby:ターミナル
bundle exec brakeman -t UnscopedFind
```

### CircleCI に導入する

**CircleCI**のセットアップ方法や、**Brakeman**の実行方法に関しては、下記の記事を参考にしてください！

https://qiita.com/muscle_coding/items/5f1c224d5bc5fe2bf8d9

https://qiita.com/piggydev/items/d3d34139a4f7be1c6e2d

https://qiita.com/piggydev/items/8addbc04982dcd40c076

## さいごに

今回は、**Brakeman**の**UnscopedFind**にのみスポットを当てましたが、本来は全てのチェック項目でアプリケーションを解析してもらい、セキュリティがしっかりとしたアプリケーションに修正することが大切です！
一つ一つチェック項目を理解し、意識して今後コードを書いていきたいと思います！
最後まで読んでいただき、ありがとうございました！

## 参考文献

https://brakemanscanner.org/

https://github.com/presidentbeef/brakeman

https://qiita.com/momo1010/items/af0023b2d065a82f715d

https://qiita.com/muscle_coding/items/5f1c224d5bc5fe2bf8d9

https://qiita.com/piggydev/items/d3d34139a4f7be1c6e2d

https://qiita.com/piggydev/items/8addbc04982dcd40c076

https://techblog.gmo-ap.jp/2021/12/06/rails-brakeman/

https://opemo.hatenablog.com/entry/2019/03/14/215228
