---
title: "【Rails】CircleCIでBrakemanを実行し、セキュリティチェックをする"
emoji: "🔒"
type: "tech"
topics: [Rails, Ruby, gem, githubactions, circleci]
published: false
publication_name: "linkedge"
---
## はじめに
お疲れ様です！
おおくまです！

今回は、「【Rails】CircleCIにBrakemanを組み込み、セキュリティチェックをする」ということで、**gem**の**Brakeman**についてまとめてみました！

少しでも皆様の参考になりますと幸いです！

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

## Brakemanとは？
[Brakeman](https://brakemanscanner.org/)は**Ruby on Rails**で書かれているアプリケーションの**セキュリティ脆弱性**を静的解析するための**gem**です！
**Brakeman**を実行することで、アプリケーションのコードをスキャンし、セキュリティ上の問題がある可能性のある箇所を特定し、警告してくれる、とても便利なツールです！
ターミナルで実行し、都度確認することも出来ますが、**CircleCI**に組み込むことで、確認し忘れることがなくなります！

**Brakeman**は、様々な観点から**セキュリティ脆弱性**を解析してくれます！
**Brakeman**がどんな種類の警告をしてくれるかは以下のサイトをご確認ください。
https://brakemanscanner.org/docs/warning_types/
