---
title: "【Rails】カラム修飾子 precision について"
emoji: "💡"
type: "tech"
topics: [Rails, Ruby, db, precision, 初学者]
published: false
publication_name: "linkedge"
---

## はじめに

お疲れ様です！
おおくまです！

今回は、「【Rails】カラム修飾子 precision について」ということで、**Ruby on Rails**のカラム修飾子である**precision**についてまとめてみました！

少しでも皆様の参考になりますと幸いです！

## 対象読者

:::message
- **Ruby on Rails**を学習中の方
- **precision**について学びたい方
:::

## 注意点

:::message alert
内容に誤りがある場合があります！
コメント等で教えていただけると幸甚です！
:::

## 環境

:::message
- Ruby：3.2.2
- Rails：7.0.8
:::

## precisionとは
**precision**とは、カラム修飾子の1つで、``decimal/numeric/datetime/time``フィールドの精度（precision）を定義するものです！
具体的には、小数点以下の桁数を指定するもので、最大6桁まで指定することができます！
この**precision**をどのように定義するのか、または定義しないのか、によって、レコードに保存される値が変わってきます！

https://railsguides.jp/active_record_migrations.html#%E3%82%AB%E3%83%A9%E3%83%A0%E4%BF%AE%E9%A3%BE%E5%AD%90

## 具体例
今回は以下の3つのテーブルを作成し、それぞれ異なるパターンで**precision**を適用し、どのように値が保存されるのか比較してみました！

```ruby:db/migrate/20240421005254_create_posts.rb
class CreatePosts < ActiveRecord::Migration[7.0]
  def change
    create_table :posts do |t|
      t.datetime :published_at, null: false
      t.timestamps
    end
  end
end
```

```ruby:db/migrate/20240421014454_create_articles.rb
class CreateArticles < ActiveRecord::Migration[7.0]
  def change
    create_table :articles do |t|
      t.datetime :published_at, precision: 0, null: false
      t.timestamps
    end
  end
end
```

```ruby:db/migrate/20240421014622_create_comments.rb
class CreateComments < ActiveRecord::Migration[7.0]
  def change
    create_table :comments do |t|
      t.datetime :published_at, precision: nil, null: false
      t.timestamps
    end
  end
end
```

```ruby:db/schema.rb
ActiveRecord::Schema[7.0].define(version: 2024_04_21_014622) do
  create_table "articles", force: :cascade do |t|
    t.datetime "published_at", precision: 0, null: false
    t.datetime "created_at", null: false
    t.datetime "updated_at", null: false
  end

  create_table "comments", force: :cascade do |t|
    t.datetime "published_at", precision: nil, null: false
    t.datetime "created_at", null: false
    t.datetime "updated_at", null: false
  end

  create_table "posts", force: :cascade do |t|
    t.datetime "published_at", null: false
    t.datetime "created_at", null: false
    t.datetime "updated_at", null: false
  end
end
```

コンソールでそれぞれの``published_at``にどのように値が保存されるか確認してみます！

```ruby:
docker-compose exec app rails c
Loading development environment (Rails 7.0.8)

[1] pry(main)> now = Time.zone.now
=> Sun, 21 Apr 2024 11:21:47.147958377 JST +09:00

[2] pry(main)> Post.create(published_at: now)
  TRANSACTION (0.1ms)  BEGIN
  Post Create (0.7ms)  INSERT INTO "posts" ("published_at", "created_at", "updated_at") VALUES ($1, $2, $3) RETURNING "id"  [["published_at", "2024-04-21 02:21:47.147958"], ["created_at", "2024-04-21 02:21:54.776314"], ["updated_at", "2024-04-21 02:21:54.776314"]]
  TRANSACTION (0.2ms)  COMMIT
=> #<Post:0x0000ffff8b6d0070
 id: 1,
 published_at: Sun, 21 Apr 2024 11:21:47.147958000 JST +09:00,
 created_at: Sun, 21 Apr 2024 11:21:54.776314000 JST +09:00,
 updated_at: Sun, 21 Apr 2024 11:21:54.776314000 JST +09:00>

[3] pry(main)> Article.create(published_at: now)
  TRANSACTION (0.3ms)  BEGIN
  Article Create (1.4ms)  INSERT INTO "articles" ("published_at", "created_at", "updated_at") VALUES ($1, $2, $3) RETURNING "id"  [["published_at", "2024-04-21 02:21:47"], ["created_at", "2024-04-21 02:21:57.198532"], ["updated_at", "2024-04-21 02:21:57.198532"]]
  TRANSACTION (0.9ms)  COMMIT
=> #<Article:0x0000ffff8b621c00
 id: 1,
 published_at: Sun, 21 Apr 2024 11:21:47.000000000 JST +09:00,
 created_at: Sun, 21 Apr 2024 11:21:57.198532000 JST +09:00,
 updated_at: Sun, 21 Apr 2024 11:21:57.198532000 JST +09:00>

[4] pry(main)> Comment.create(published_at: now)
  TRANSACTION (0.2ms)  BEGIN
  Comment Create (1.3ms)  INSERT INTO "comments" ("published_at", "created_at", "updated_at") VALUES ($1, $2, $3) RETURNING "id"  [["published_at", "2024-04-21 02:21:47.147958"], ["created_at", "2024-04-21 02:21:59.266544"], ["updated_at", "2024-04-21 02:21:59.266544"]]
  TRANSACTION (1.0ms)  COMMIT
=> #<Comment:0x0000ffff8b5e3040
 id: 1,
 published_at: Sun, 21 Apr 2024 11:21:47.147958377 JST +09:00,
 created_at: Sun, 21 Apr 2024 11:21:59.266544000 JST +09:00,
 updated_at: Sun, 21 Apr 2024 11:21:59.266544000 JST +09:00>
```

``now``と、それぞれの``published_at``の値はこのようになりました！

- ``now``
  - ``Sun, 21 Apr 2024 11:21:47.147958377 JST +09:00``

- ``Post``
  - ``Sun, 21 Apr 2024 11:21:47.147958000 JST +09:00``
  - **precision**を特に指定しなかった場合は、小数点以下6桁まで値が保存されています！
  - 私の環境では、**precision**を指定していない場合は、デフォルトで``precision: 6``と設定されたみたいです！

- ``Article``
  - ``Sun, 21 Apr 2024 11:21:47.000000000 JST +09:00``
  - こちらは、``precision: 0``と指定しているので、小数点以下は値が保存されていません！

- ``Comment``
  - ``Sun, 21 Apr 2024 11:21:47.147958377 JST +09:00``
  - こちらは、``precision: nil``と指定した結果、このようになりました！
  - 調べたところ、``precision: nil``と指定した場合は、各環境のデータベースのデフォルトに設定されるようです！
  - コンソール上では、小数点以下9桁までしか表示されていないので、``now``と保存されている値が同じに見えますが、比較してみると値は違うみたいです！
  ```
  [5] pry(main)> now == Comment.first.published_at
    Comment Load (0.2ms)  SELECT "comments".* FROM "comments" ORDER BY "comments"."id" ASC LIMIT $1  [["LIMIT", 1]]
  => false
  ```

このように**precision**をどのように適用するのかによって保存される値が変わってきますので、テーブル作成やカラム追加の際には注意が必要です！

また、**precision**は、**Rails**のバージョンによっても、挙動が変わってくるので、下記の記事を参考にしてみてください！

https://zenn.dev/bisque/scraps/6ff905748302e4

## さいごに

今回、**precision**についてまとめてみましたが、私はこれを知らなかったために、通るはずの**RSpec**が通らず苦労しました。笑
今後、テーブル作成やカラム追加の際には注意したいと思います！
最後まで読んでいただき、ありがとうございました！

## 参考文献

https://kossy-web-engineer.hatenablog.com/entry/2020/12/13/111823
