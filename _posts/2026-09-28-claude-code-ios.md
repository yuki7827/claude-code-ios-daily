---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-09-28"
date: 2026-09-28 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート
- 本日時点で新規リリースなし（最新は [v2.1.283（2026-09-25）](https://github.com/anthropics/claude-code/releases/tag/v2.1.283) — 詳細は昨日の記事参照）

## 🛠 GitHub の動き

- **[XcodeBuildMCP → MobileBuildMCP へリネーム（PR #538、2026-09-23 マージ）](https://github.com/getsentry/MobileBuildMCP/pull/538)** — XcodeBuildMCP が **MobileBuildMCP** に改名された。npm パッケージ名・CLI バイナリ・環境変数（`MOBILEBUILDMCP_*`）・設定ディレクトリ（`.xcodebuildmcp/` → `.mobilebuildmcp/`）・MCP ツール名（`mcp__XcodeBuildMCP__*` → `mcp__MobileBuildMCP__*`）がすべて変更されるため**破壊的変更**。旧ドメイン xcodebuildmcp.com はすでに失効しており、スキーマ URL も GitHub raw コンテンツに移行済み。既存ユーザーはインストールと設定を更新する必要がある。

- **[mcs-cli/ios: XcodeBuildMCP → MobileBuildMCP 移行 PR（PR #3、2026-09-24 マージ）](https://github.com/mcs-cli/ios/pull/3)** — `mcs pack add mcs-cli/ios` + `mcs sync` で iOS 環境を構築している場合、PR #3 適用後に `mcs sync` を再実行すると旧 XcodeBuildMCP サーバーが自動削除され `.mobilebuildmcp/config.yaml` が生成される。設定の手動書き換えは不要。

- **[Issue #97552（2026-09-27 作成 / OPEN）— モバイルアプリがラップトップでの作業後に古いセッション内容を表示する](https://github.com/anthropics/claude-code/issues/97552)** — ラップトップで Claude Code を使用した 1 時間後に iPhone でクラウドセッションを開くと、今朝の会話ではなく前夜のスナップショットが表示される。定期スケジュール（Routine）と手動操作が混在するセッションで特に発生しやすい可能性。iOS アプリからリアルタイムに進捗を確認している場合に注意。

- **[Issue #97410（2026-09-26 作成 / OPEN）— Remote Control モバイル: ターミナルのプロンプト候補が入力欄に表示されない](https://github.com/anthropics/claude-code/issues/97410)** — デスクトップ端末では入力欄にコンテキスト依存のコマンド候補（例: `Try 'create a util logging.py that...'`）が表示されるが、iOS アプリの Remote Control 経由では表示されない。提案: Remote Control プロトコルに `promptSuggestion` フィールドを追加してモバイル UI にも反映させる。

- **[Issue #97402（2026-09-26 作成 / OPEN）— Remote Control（モバイル）: コーディング不要なチャットでも GitHub アカウント接続を強要される](https://github.com/anthropics/claude-code/issues/97402)** — GitHub と無関係の質問を送ろうとした場合にも GitHub アカウントの接続プロンプトが表示されブロックされる。プライバシー上の懸念もあり、GitHub 操作を伴わないセッションでは接続を不要にすべきとの Feature Request。

## 📝 日本語コミュニティ
- **[iPhoneからClaude Codeを操作できるアプリ「NeriTerm」をリリースしました（Qiita / nerikeshi 氏）](https://qiita.com/nerikeshi/items/6e50dc84a390feedbc1c)** — Swift 製の iOS/iPadOS アプリ。Mac 側でコンパニオンサーバー（Node.js）を起動し、Tailscale VPN 経由で暗号化接続を確立することで iPhone/iPad からターミナルを操作できる。公式 Remote Control とは独立したサードパーティ実装。App Store に公開済み。

## 🌐 海外コミュニティ / Tips
- **[iTerm2 3.7 — iOS companion app「iTerm2 Buddy」と Claude Code Session Status 統合（2026-09-08 リリース）](https://iterm2.com/claude-code-integration.html)** — iTerm2 3.7 は QR コードスキャン＋ Noise 暗号化チャネルで Mac とペアリングする iOS アプリ「iTerm2 Buddy」を追加。Claude Code 向け機能として **Session Status**（`it2` コマンドや制御シーケンスでタブのサブタイトル・カラー・アイコンをセッション状態に応じて更新）と **Orchestration**（全セッションを横断して Claude Code を操作できる AI チャット）を導入。コードレビューコメントを Claude Code に直接送り返す Clippings 連携も実装。

## 💡 今日のおすすめ実践 Tip
**MobileBuildMCP への移行手順（XcodeBuildMCP ユーザー向け）**

`mcs-cli/ios` 経由で構築した環境なら `mcs sync` を再実行するだけで自動移行できる。手動でセットアップしていた場合は以下の手順で対応する:

1. `brew uninstall xcodebuildmcp && brew tap getsentry/mobilebuildmcp && brew install mobilebuildmcp` で CLI を更新
2. `.claude/settings.json` の MCP サーバー定義を `xcodebuildmcp` → `mobilebuildmcp` に変更
3. プロジェクトの `.xcodebuildmcp/` ディレクトリを `.mobilebuildmcp/` にリネームし、中の設定ファイルも更新
4. 環境変数名も `XCODEBUILDMCP_*` → `MOBILEBUILDMCP_*` に変更

`mcp__XcodeBuildMCP__build_ios` 等のツール名が変わるので、CLAUDE.md や hooks で旧ツール名を参照している場合は合わせて書き換えること。
