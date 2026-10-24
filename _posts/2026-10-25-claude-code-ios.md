---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-10-25"
date: 2026-10-25 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [v2.1.310（2026-10-23）— Monitor ツールが Bedrock / Vertex / Foundry に対応](https://code.claude.com/docs/en/changelog) — Monitor ツールが Amazon Bedrock・Google Cloud Vertex・Microsoft Foundry 環境でも使えるようになった。加えて Cloud sessions と Remote Control のパーミッションが Enterprise カスタムロールに割り当て可能に。非インタラクティブセッションで MCP ツール呼び出しがタイムアウトした場合、Claude に「回答なし」を通知し無限再試行を防ぐ修正も含む。AWS Bedrock 経由で Claude Code を CI 運用している iOS チームには恩恵が大きい。
- [v2.1.309（2026-10-22）— `/claude-api` スキルが `multiagent_20261001` 対応・`sed -i` が標準 Bash プロンプト経由に](https://code.claude.com/docs/en/changelog) — `/claude-api` スキルで生成する Managed Agents チームが新 API バージョン `multiagent_20261001` ベースになった。また `sed -i` が標準 Bash プロンプト経由で実行されるよう変更され、iOS ビルドスクリプトでの挙動が安定化。`claude ultrareview` はワークスペーストラストが必要になり、62 件のバグ修正が含まれる。

## 🛠 GitHub の動き

- [getsentry/XcodeBuildMCP #446 — Xcode 27 beta で AXe が SimulatorKit.framework をロードできない問題](https://github.com/getsentry/XcodeBuildMCP/issues/446) — Xcode 27 beta で SimulatorKit.framework の格納先が変更されたため、XcodeBuildMCP にバンドルされた AXe が読み込めずシミュレーター UI 自動化コマンドが失敗する問題。XcodeBuildMCP v2.6.2 / AXe v1.7.1 で再現確認済み（Xcode 27.0 Build 27A5194q）。Xcode 27 GA に移行した iOS チームは v2.7.0 以降への更新を確認すること。

## 📝 日本語コミュニティ

- 該当なし（2026-10-25 時点で Zenn・Qiita に Claude Code × iOS / Swift / Xcode 関連の新規記事は確認できず）

## 🌐 海外コミュニティ / Tips

- [getsentry/XcodeBuildMCP — Sentry が XcodeBuildMCP を買収・リポを移管](https://github.com/getsentry/XcodeBuildMCP) — 2026年2月に Sentry が cameroncooke/XcodeBuildMCP を買収し、getsentry 組織に移管。旧 URL からは 301 リダイレクトで転送される。Claude Code 設定で MCP サーバーを登録している場合、URL や npm パッケージ名（`xcodebuildmcp`）は変わらないが、インストール手順が `brew tap getsentry/xcodebuildmcp && brew install xcodebuildmcp` に更新された。
- [mcp.directory — XcodeBuildMCP Complete Guide 2026：Claude Code と XcodeBuildMCP の組み合わせ解説](https://mcp.directory/blog/xcodebuildmcp-complete-guide-2026) — Claude Code + XcodeBuildMCP の連携セットアップ手順をまとめた解説記事。追加コマンドは `claude mcp add XcodeBuildMCP -- npx -y xcodebuildmcp@latest mcp` で、ビルド・テスト・シミュレーター起動・デバイスデプロイの各ツールを Claude から操作できる。診断ツール `npx --package xcodebuildmcp@latest xcodebuildmcp-doctor` で設定の正常性を確認できる点も紹介されている。

## 💡 今日のおすすめ実践 Tip

**v2.1.309 の `sed -i` 変更で iOS ビルドスクリプトのトラブルを回避する**

v2.1.309 以降、`sed -i` が標準 Bash プロンプト経由で実行されるよう変更された。iOS 開発では `Info.plist` のバージョン書き換えや `xcconfig` 値の差し替えなど、`sed -i` を使ったビルドスクリプトが多い。以前は Claude が sandboxed 環境でこれを実行しようとして権限エラーや挙動の不一致が起きるケースがあったが、今回の変更で通常の Bash コマンドと同等に扱われる。

**典型的な iOS ユースケース:**

```bash
# CFBundleShortVersionString をビルド番号で自動更新（ローカルスクリプト例）
sed -i '' "s/MARKETING_VERSION = .*/MARKETING_VERSION = ${NEW_VERSION};/" MyApp.xcconfig

# Info.plist の URLScheme を環境ごとに書き換え（Dev / Staging / Prod）
sed -i '' "s/myapp-dev/myapp-${ENV}/g" MyApp/Info.plist
```

Claude Code に「バージョン番号を 2.3.1 に更新して」と依頼した際に、上記のような `sed -i` コマンドが含まれるスクリプトを実行するケースで、v2.1.309 以降はより安定して動作する。

**注意点:**
- macOS の `sed -i` は BSD 版のため `-i ''`（空の拡張子）が必要。Claude に任せる場合は CLAUDE.md に `sed -i'' '' ...` 形式を明示しておくと確実
- `allowedMcpServers` の変数展開ルールも同バージョンで修正されているため、`${ENV_VAR}` を使った MCP サーバー設定も見直しを推奨
