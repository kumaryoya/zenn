---
title: "【Ruby】自作 Gem の最新バージョンをリリースする"
emoji: "💎"
type: "tech"
topics: [ruby, gem, 個人開発, バージョン管理, バージョンアップ]
published: false
publication_name: "linkedge"
---

## はじめに

2年ほど前ですが、プログラミングスクールでプログラミングを学んでいる際に、自作の Gem を作成し、公開したことがあります。

内容としては非常にシンプルなもので、メソッドを呼び出すと、某漫画に出てくる悪魔の実の名前をランダムに返すというものです。

作成した日から一切更新しておらず、その間に新しい悪魔の実も登場しているため、悪魔の実を追加し、最新バージョンとしてリリースすることにしました。

本記事では、備忘録も兼ねて、自作 Gem の最新バージョンをリリースするまでの流れをご紹介します。

![](https://storage.googleapis.com/zenn-user-upload/5b63896f0533-20251025.jpg =400x)

## 注意点

:::message alert
- 内容に誤りが含まれている可能性があります
- コメントなどでご指摘いただけると幸いです
:::

## 対象の Gem

本記事では、以下の Gem の最新バージョンをリリースしていきます。

https://github.com/kumaryoya/devil_fruit

https://rubygems.org/gems/devil_fruit

## 手順

現在、最新バージョンは `0.2.0` となっているため、`0.3.0` としてリリースしていきます。

### 1. Gem のコードを修正する

まずは、Gem のコードを修正していきます。

`lib/devil_fruit/format_devil_fruit.rb` に新しい悪魔の実を追加しました。

```diff ruby:lib/devil_fruit/format_devil_fruit.rb
module DevilFruitGem
  module FormatDevilFruit
    def self.random_devil_fruit
      devil_fruits = [
        "ゴムゴムの実",
        "バラバラの実",
        "スベスベの実",
        # ... 中略 ...
        "ネコネコの実 古代種 モデル サーベルタイガー",
        "ヒトヒトの実 幻獣種 モデル ニカ",
        "モリモリの実",
      + "シクシクの実",
      + "ワプワプの実",
      + "リキリキの実",
      + "ウマウマの実 幻獣種 モデル ペガサス",
      + "ノミノミの実",
      + "シマシマの実",
      + "ガブガブの実",
      + "ムチムチの実",
      + "グニョグニョの実",
      + "ノリノリの実",
      ]
      devil_fruits.sample
    end
  end
end
```

### 2. バージョン番号を更新する

次に、バージョン番号を更新します。

`lib/devil_fruit/version.rb` のバージョン番号を `0.3.0` に更新しました。

```diff ruby:lib/devil_fruit/version.rb
module DevilFruit
+ VERSION = "0.3.0"
end
```

### 3. Gem をビルドする

次に、Gem をビルドします。

```ruby
bundle exec rake build
```

ビルドが成功すると、`pkg/devil_fruit-0.3.0.gem` というファイルが生成されます。

### 4. Gem をリリースする

最後に、Gem をリリースします。

```ruby
bundle exec rake release
```

メールアドレスとパスワードの入力を求められるので、RubyGems.org に登録しているアカウント情報を入力します。

認証が成功すると、Gem がリリースされます。

以上で、自作 Gem の最新バージョンをリリースする手順は完了です。

## 使ってみる

最新バージョンをインストールして、動作を確認してみます。

まず、現在インストールされているバージョンを確認します。

```ruby
gem list devil_fruit

*** LOCAL GEMS ***

devil_fruit (0.2.0)
```

インストール前は、`0.2.0` がインストールされていることが確認できました。

最新バージョンをインストールします。

```ruby
gem install devil_fruit

Fetching devil_fruit-0.3.0.gem

Successfully installed devil_fruit-0.3.0

1 gem installed
```

インストール後、バージョンを確認します。

```ruby
gem list devil_fruit

*** LOCAL GEMS ***

devil_fruit (0.3.0, 0.2.0)
```

`0.3.0` がインストールされていることが確認できました。

追加した悪魔の実が含まれているか、動作を確認してみます。

```ruby
irb
irb(main):001> require 'devil_fruit'
=> true
irb(main):002> DevilFruitGem::FormatDevilFruit.random_devil_fruit
=> "シクシクの実"
```

無事に追加した悪魔の実が含まれていることが確認できました。

## まとめ

自作 Gem の最新バージョンをリリースする手順をご紹介しました。

とても簡単に最新バージョンをリリースすることができました。

今後も適宜、新しい悪魔の実が登場した際には、最新バージョンをリリースしていきたいと思います。

最後までご覧いただき、ありがとうございました。
