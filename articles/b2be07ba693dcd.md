---
title: "【Ruby】自作 Gem の最新バージョンをリリースする"
emoji: "💎"
type: "tech"
topics: [ruby, gem, 個人開発, バージョン管理, バージョンアップ]
published: false
publication_name: "linkedge"
---

## はじめに

2年ほど前ですが、プログラミングスクールでプログラミングを学んでいる間に、自作の Gem を作成し、公開したことがあります。

内容としては非常にシンプルなもので、メソッドを呼び出すと、某漫画に出てくる悪魔の実の名前をランダムに返すというものです。

作成した日から一切更新しておらず、その間に新しい悪魔の実も登場しているため、悪魔の実を追加し、最新バージョンとしてリリースすることにしました。

本記事では、備忘録も兼ねて、自作 Gem の最新バージョンをリリースするまでの流れをご紹介します。

![](https://storage.googleapis.com/zenn-user-upload/5b63896f0533-20251025.jpg =1000x)

## 注意点

:::message alert
- 内容に誤りが含まれている可能性があります
- コメントなどでご指摘いただけると幸いです
:::

## 環境

本記事で使用する環境は以下のとおりです。

:::message
- Visual Studio Code Insiders
- GitHub Copilot
- Devin
- GitHub MCP Server
- Slack MCP Server
:::

![](https://storage.googleapis.com/zenn-user-upload/99c677aa62a1-20250428.png =600x)

## 前提

本記事では、以下を前提とします。

:::message
- Visual Studio Code または、Visual Studio Code Insiders がセットアップされていること
- GitHub Copilot がセットアップされており、エージェントモードが使用可能であること
- Devin がセットアップされていること
:::
