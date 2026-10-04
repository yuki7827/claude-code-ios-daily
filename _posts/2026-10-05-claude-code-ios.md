---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-10-05"
date: 2026-10-05 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- **[v2.1.289（2026-10-03）リリース](https://github.com/anthropics/claude-code/releases/tag/v2.1.289)** — iOS 開発に関係する主な変更点は以下の通り。**`agent.spawn` でチームメイトをプラグインから生成できるようになり、`$.agent.list()` に `idle` / `waiting` ステートが追加**（複数エージェントで iOS テストスイートや並列ビルドを並走させるワークフローに有用）。セキュリティ修正として **`Read` の deny ルールが IDE 経由でシンボリックリンク経由に @-mention されたファイルにも適用されるように**なり、管理マシン上で user-installed mod の承認が複合シェルコマンドのネストされたルールを上書きしていたバグも修正。**ターミナルが深くネストした `${` 代入で凍結するバグを修正**（Swift テンプレートリテラル等で発生しやすかった）。プラグイン管理では `claude plugin validate` が Anthropic マーケットプレイスのプラグインで失敗していた問題、ローカルフォルダマーケットプレイスからインストールしたプラグインで `plugin list` / `plugin eval` / `plugin update` が古いコピーを表示していた問題を修正。VSCode 拡張では 2.1.288 で導入した `claude auth status` 変更（サインアウト頻度増加の原因）をリバート。

## 🛠 GitHub の動き

- **[Issue #96941（2026-09-25 作成 / OPEN）— iOS Simulator パネル・ツールが iPhone Duo のフォールド状態と向きを認識しない（座標ずれ・スクリーンショット失敗）](https://github.com/anthropics/claude-code/issues/96941)** — macOS 27.2 / Xcode 27.1 beta / Device Hub 環境で、**フォールダブル iPhone Duo シミュレーターを展開（unfold）してランドスケープにしても、Claude Code の iOS Simulator ツールが古い折り畳み時の座標（466×678 pt）を返し続ける**。`attach` でパネルが 90° 横向きに表示される、`screenshot` が `captureFailed` で失敗する、`inspect` が "not available right now" を返すという 3 つの問題が複合的に発生。暫定回避策: `xcrun simctl io <udid> screenshot` で正しい向きの画像を取得できる（2853×2007 px のアンフォールドランドスケープが正常取得可）。Issue #95466（tap injection 無効化）に関連する可能性あり。ラベル: `bug`, `area:desktop`, `platform:macos`。

## 📝 日本語コミュニティ

- **[AI に iOS アプリを実装させるとき、触らせてはいけないファイルと守り方（Qiita: yasu-ya）](https://qiita.com/yasu-ya/items/c5b28fe4781d96723b20)** — Claude Code 等 AI エージェントに iOS 開発を任せる際、CI パイプライン定義・Signing ファイル・`Package.resolved`・`*.entitlements` など**変更させると破壊的になるファイル群を整理し、CLAUDE.md の `deny` ルールや `.claude/settings.json` で守る方法**を解説。Xcode 26.6 + GitHub Actions 環境で検証済みの実践的な内容。

- **[Claude Code 標準 Skill を iOS アプリ開発でどう使うか：公式ドキュメントを根拠に整理する（Qiita: 4q_sano）](https://qiita.com/4q_sano/items/0ecf0fc7353d751f601c)** — `/code-review` でリリース前の Swift ファイルを一括レビュー、`/simplify` でリファクタリング候補を抽出、`/run` でシミュレーター上の動作確認を Claude に委ねるといった**公式スキルを iOS 開発の各フェーズにマッピングする**整理記事。

## 🌐 海外コミュニティ / Tips

- **[v2.1.289 の `agent.spawn` で iOS 並列開発チームを構成する（Claude Code Agent Teams ガイド各所より）](https://promptessor.com/blog/claude-code-agent-teams-examples-and-multi-agent-workflows-for-parallel-development-in-2026)** — v2.1.289 で追加された `agent.spawn` をプラグインから呼び出すことで、**「UI 担当エージェント（SwiftUI）」「ネットワーク層担当エージェント（async/await + Combine）」「テスト担当エージェント（XCTest / Maestro）」を独立したコンテキストウィンドウで並列実行**するマルチエージェント iOS 開発ワークフローが実現可能に。ただし `-p` フラグや Agent SDK での非インタラクティブセッションでは、設定で `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1` にしていてもチームメイトは生成されないことに注意（インタラクティブセッション限定の実験的機能）。

## 💡 今日のおすすめ実践 Tip

**「`claude plugin validate` 修正を機に MobileBuildMCP と iOS ワークフロープラグインを棚卸しする」**

v2.1.289 で `claude plugin validate` が Anthropic マーケットプレイスのプラグインを誤って弾いていたバグが修正されました。これを機に、プロジェクトにインストール済みのプラグインの動作確認をまとめて行うのがおすすめです。

```bash
# インストール済みプラグインを一覧表示（stale コピー問題も今回修正済み）
claude plugin list

# 各プラグインの検証
claude plugin validate MobileBuildMCP
claude plugin validate sim-set   # インストール済みの場合

# アップデートの確認（stale コピー表示バグも修正済み）
claude plugin update
```

特に MobileBuildMCP を使っている場合、旧 `.xcodebuildmcp/` ディレクトリが残っているとコンフリクトの原因になります。

```bash
# 旧設定ディレクトリの確認（あれば削除）
ls -la .xcodebuildmcp/
rm -rf .xcodebuildmcp/

# 新設定ファイルの確認
cat .mobilebuildmcp/config.yaml
```

`plugin validate` でエラーが出た場合、多くは設定ファイルのパス変更や `config.yaml` のキー名変更が原因です。エラーメッセージを Claude に貼り付けると、適切な `config.yaml` の修正案を提示してくれます。
