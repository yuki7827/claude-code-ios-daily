---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-05-03"
date: 2026-05-03 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Apple's Xcode now supports the Claude Agent SDK](https://www.anthropic.com/news/apple-xcode-claude-agent-sdk) — Xcode 26.3 が Claude Agent SDK に正式対応。サブエージェント・バックグラウンドタスク・プラグインを含む Claude Code の全機能を Xcode 内から利用可能に。
- [Claude is now generally available in Xcode](https://www.anthropic.com/news/claude-in-xcode) — Claude Sonnet 4 が Xcode 26 で GA。Pro/Max プランおよびチーム・エンタープライズの有料シートで利用開始。
- [Claude Code Updates – May 2026](https://releasebot.io/updates/anthropic/claude-code) — MCP・プラグインのワークフロー高速化と、メモリリーク・クラッシュ・OAuth 等の安定性修正を含む大型アップデート。

## 🛠 GitHub の動き

- [ios-simulator-skill](https://github.com/conorluddy/ios-simulator-skill) (⭐909) — Claude Code 用 iOS Simulator スキル。xcodebuild のラッパーとしてコンテキスト節約に活用でき、本日も更新あり。
- [swift-ios-skills](https://github.com/dpearson2699/swift-ios-skills) (⭐515) — iOS 26+/Swift 6.3/SwiftUI 向けエージェントスキル集。84 スキルを `--all` フラグで一括インストール可能。Liquid Glass・Foundation Models など最新 API に対応し本日更新。
- [memorydetective](https://github.com/carloshpdoc/memorydetective) (⭐6) — `.memgraph`/`.trace` ファイルを解析する iOS メモリリーク調査 MCP サーバー。27 ツールでリテインサイクル分類と CI 統合をサポート。
- [xcstrings-mcp](https://github.com/Murzav/xcstrings-mcp) (⭐2) — iOS/macOS `.xcstrings` ローカライズのフルライフサイクル（作成→抽出→翻訳→検証→エクスポート）を 26 MCP ツールで自動化。Rust 製。
- [notchy](https://github.com/graphimedianex-design/notchy) — Claude Code を MacBook のノッチに常駐させるフローティングターミナル（Swift 製）。Xcode プロジェクト検出・マルチセッションタブ・ライブステータス表示つき、本日更新。

## 📝 日本語コミュニティ

- [Xcode MCP×Claude Codeプラグインで、iOSビルドを自動化する](https://zenn.dev/kyoichi/articles/claude-code-plugin-xcode-mcp-hybrid) — Apple 純正 Xcode MCP と XcodeBuildMCP を Claude Code プラグインとして組み合わせ、ビルド〜テスト〜プレビューを自動化する構成例。
- [XcodeBuildMCP×Claude Codeスキルシステムで、iOSビルドを自動化する](https://zenn.dev/kyoichi/articles/claude-code-xcodebuildmcp-ios-build) — スキルシステムを活用した iOS ビルド自動化の詳細ガイド。
- [Automating iOS App Testing with Claude Code and XcodeBuildMCP](https://zenn.dev/shimo4228/articles/xcodebuildmcp-ios-verification) — UI 操作からスクリーンショット検証まで、Claude Code + XcodeBuildMCP による iOS テスト自動化の実践レポート。
- [【速報】AppleがXcodeにClaude搭載！AIがアプリを自動で作る時代が本当に来た](https://qiita.com/emi_ndk/items/771b2b3b31642c526224) — Xcode 26.3 エージェンティックコーディングの概要と iOS 開発への影響をまとめた Qiita 記事。

## 🌐 海外コミュニティ / Tips

- [Two MCP Servers Made Claude Code an iOS Build System](https://blakecrosley.com/blog/xcode-mcp-claude-code) — XcodeBuildMCP（59 ツール）と Apple 純正 Xcode MCP（20 ツール）の 2 本立てで Claude Code を iOS ビルドシステム化した構成解説。
- [SwiftUI Agent Skill – Hacking with Swift](https://www.hackingwithswift.com/articles/282/swiftui-agent-skill-claude-codex-ai) — Paul Hudson による SwiftUI エージェントスキル。Claude Code・Codex・Cursor など 40+ エージェントで共通利用可能。
- [Reduce iOS Development Time by 60% with Claude Code](https://medium.com/@osmandemiroz/reduce-ios-development-time-by-60-with-claude-code-86a4e9d864ca) — Claude Code を iOS 開発フローに組み込み開発時間を 60% 短縮した事例報告。

## 💡 今日のおすすめ実践 Tip

**`memorydetective` MCP で Instruments のリテインサイクルをゼロコンテキスト切り替えで調査する**

[memorydetective](https://github.com/carloshpdoc/memorydetective) は Instruments で取得した `.memgraph` や `.trace` ファイルを Claude Code から直接解析できる MCP サーバーです。`claude mcp add memorydetective` で登録すると、「このクラスのリテインサイクルを調べて」とプロンプトするだけでツールが `.memgraph` を読み込み、原因となるオブジェクトグラフを構造化 JSON で返します。CI 統合ツールも同梱しているため、Pull Request ごとにメモリ回帰を自動検出するパイプラインも構築できます。iOS アプリのメモリ問題を Xcode を離れずに追跡できる点が大きな利点です。
