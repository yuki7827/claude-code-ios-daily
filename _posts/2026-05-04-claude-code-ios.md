---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-05-04"
date: 2026-05-04 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート
- 本日は新規の公式アップデートなし

## 🛠 GitHub の動き

- [XcodeBuildMCP v2.5.0-beta.1](https://github.com/getsentry/XcodeBuildMCP/releases) — 4/30 公開のベータ大型更新。MCP クライアント・CLI 全体に「Structured Outputs」を導入し、JSON スキーマ検証済みの機械可読結果を返すように。tvOS/watchOS/visionOS に対応したプラットフォーム自動検出セットアップウィザード、ビルドのライブ進捗表示、シミュレーター向けキーボード操作ツールも追加。シェルインジェクション・パストラバーサルの脆弱性修正も含む。
- [keskinonur/claude-code-ios-dev-guide](https://github.com/keskinonur/claude-code-ios-dev-guide) (⭐651) — Claude Code CLI を iOS 開発向けに最適化するセットアップ全集。PRD 駆動ワークフロー・`ultrathink` 拡張思考・XcodeBuildMCP 統合・Swift リント用 Hooks・カスタムスラッシュコマンドまで網羅した実践ガイド。

## 📝 日本語コミュニティ

- [Claude Code on the Web でスマホだけでアプリを開発しリリースしてみた](https://zenn.dev/ruwatana/articles/claude-code-on-the-web-for-app-development) — PC 不要で Claude Code on the Web のみを使い、Xcode Cloud でビルド・TestFlight 配信まで完結させた実録レポート。
- [非エンジニアがClaude Codeだけで、iOSアプリをApp Storeに出した話](https://zenn.dev/naoki_dev/articles/098a689c4acdd5) — 環境構築から約3週間で Sudoku One を 148 カ国の App Store にリリース。CocoaPods・Ruby バージョン管理など非エンジニア視点の詰まりどころと、7言語メタデータを Claude に翻訳させた手順を詳述。

## 🌐 海外コミュニティ / Tips

- [Agentic Coding in Xcode 26.3 with Claude Code and Codex | Swiftjective-C](https://swiftjectivec.com/Agentic-Coding-Codex-Claude-Code-in-Xcode/) — Claude Code 未経験者にも「ここから始めれば良い」と勧める実践レビュー。スキルフォルダと `config.toml` で MCP を追加する手順も紹介。
- [Agentic Coding in Xcode 26.3 — Part 3: iOS 18 ターゲット編](https://medium.com/@chinthaka01/agentic-coding-in-xcode-26-3-8b62c1e46bf2) — iOS 18 以降をターゲットにした Claude Agent + Xcode 26.3 の実践レポート。SwiftUI Preview を取得してビジュアル検証しながら反復する工程を解説（2026/03）。
- [Meet agentic coding in Xcode — Apple Developer Tech Talk](https://developer.apple.com/videos/play/tech-talks/111428/) — Apple 公式の Tech Talk 動画。MCP 経由でエージェントが Xcode のビルド・テスト・Preview キャプチャをどう呼び出すか、仕組みを図解つきで説明。

## 💡 今日のおすすめ実践 Tip

**XcodeBuildMCP v2.5.0-beta.1 の Structured Outputs で CI ログをパースする**

v2.5.0-beta.1 から MCP ツールの戻り値が JSON スキーマ検証済みの構造化オブジェクトに統一されました。これにより Claude Code へのフィードバックループが安定し、「ビルド結果を解釈してエラーを修正してください」というプロンプトが以前より正確に機能します。また JSON で返るため、GitHub Actions の後続ステップで `jq` を使ってエラー件数を取り出したり、Slack 通知に整形するなど CI 側での加工も容易になります。試すには `brew upgrade xcodebuildmcp` で beta チャンネルに上げた後、`xcodebuildmcp init` で既存プロジェクトの設定を自動更新できます。
