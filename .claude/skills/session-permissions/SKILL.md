---
name: session-permissions
description: 現在のセッションでユーザーに承認を求めたツール呼び出しを抽出して整理し、危険度を解説した上で .claude/settings.json の allow リストに追加する。「権限を追加して」「セッションで使った権限を整理して」「設定を更新して」と依頼されたときに使用する。
---

現在のセッションで承認プロンプトが発生した（または発生する可能性が高かった）ツール呼び出しを整理し、ユーザーの判断のもとで `.claude/settings.json` に allow ルールを追加する。

## 対象とする操作

このセッションで実行された以下を抽出する:

- `Bash` ツールの実行コマンド（`npx zenn`, `git`, `gh`, `curl`, `find`, `sed`, `awk` など）
- `Edit` / `Write` ツールの呼び出し
- `WebFetch` / `WebSearch` の呼び出し
- MCP ツール（`mcp__*`）の呼び出し

`Read` / `Glob` / `Grep` / `TodoWrite` / `ToolSearch` など、通常承認不要なツールは対象外。

## 既存設定との突合

`.claude/settings.json` を読み、既に許可されているルールを把握する。

- 既存ルールに**完全に包含される操作**は除外する（例: `Bash(git *)` が既にあれば `Bash(git status*)` は不要）
- 既存ルールの**プレフィックスがマッチしない場合のみ**新規追加候補とする（例: `npx zenn preview` は許可済みでも `npx zenn new:article` は別ルールが必要）

## 分類とリスク解説

抽出した操作を以下のカテゴリに分類して提示する:

### 1. 読み取り系（低リスク）
- `git status` / `git log` / `git diff` / `git show` / `git ls-files`
- `gh pr view` / `gh pr diff` / `gh pr list`
- `ls` / `find` / `head` / `wc` / `sed -n` / `awk`
- `npx zenn list:articles` / `npx zenn list:books`

→ ファイル・状態の閲覧のみ。**追加して問題が起きにくい**。

### 2. zenn-cli / 記事作成系（中リスク）
- `npx zenn preview`（ローカルプレビューサーバ起動）
- `npx zenn new:article` / `npx zenn new:book`（`articles/` `books/` にファイルを新規作成）

→ ローカルでサーバを立てる、ファイルを生成するなどの副作用がある。**頻繁に書く運用なら追加価値あり**。

### 3. ファイル変更系（中〜高リスク）
- `Edit` / `Write` ツール

→ リポジトリ内のあらゆるファイルを書き換え可能になる。`articles/*.md` の編集が中心ならメリット大。**機密ファイル（仮にあれば）への波及に注意**。

### 4. 外部通信・破壊的操作系（高リスク）
- `curl` / `WebFetch`（Zenn API や外部ページ取得）
- `git push` / `git reset --hard` / `rm -rf`
- `gh pr create` / `gh issue create` など外部書き込み

→ 影響範囲が広い。**個別承認のままが安全**。allow に入れる場合はパターンを最小限に絞る（例: `Bash(curl -s https://zenn.dev/api/*)` のように URL を限定）。

## 提示と確認のフロー

1. **抽出結果を分類して提示**

    各カテゴリで追加候補となるルールをリストアップ。各ルールに「セッション内で何回使用したか」を併記すると判断材料になる。

2. **粒度の選択肢を提示**

    最低 3 段階を提示する:
    - **最小**: カテゴリ 1（読み取り系）のみ
    - **中間**: カテゴリ 1 + 2（zenn-cli / 記事作成系）
    - **最大**: カテゴリ 1 + 2 + 3（ファイル変更含む）

    カテゴリ 4 は明示的に依頼されない限り提案しない。

3. **保存先の確認**

    曖昧な場合は確認:
    - `.claude/settings.json`（チーム共有・コミット対象）
    - `.claude/settings.local.json`（個人設定・gitignore 対象）

    デフォルトは `.claude/settings.json`。

## 設定ファイルの更新

1. 対象ファイルを **必ず先に Read** する
2. 既存の `permissions.allow` 配列を**保持したまま追加**する（置換しない）
3. ルール記述順は以下を意識すると読みやすい:
    - ツール名のみ（`Read`, `Edit`, `Write`）を先頭
    - `Bash(...)` を機能ごとにグループ化（zenn / git / gh / utility / web）
4. Edit ツールで `permissions.allow` 配列の末尾要素の直後に挿入する
5. 更新後にもう一度 Read して JSON 構文が壊れていないことを目視確認する

## 結果の報告

追加したルールをカテゴリごとにまとめて報告する。例:

```
.claude/settings.json に以下を追加しました:

【ツール一括許可】
- Edit / Write

【git 読み取り系】
- Bash(git status*) / git log* / git diff*

【zenn-cli】
- Bash(npx zenn preview) / Bash(npx zenn list:articles)
```

**やってはいけないこと**:

- ユーザーの確認なしにカテゴリ 3・4 を追加する
- 既存の allow ルールを上書き・並べ替える（追加のみ）
- セッションで実際に使われていない操作を「念のため」追加する（実使用に基づくこと）
