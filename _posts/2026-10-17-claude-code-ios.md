---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-10-17"
date: 2026-10-17 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- 該当なし（2026-10-17 時点で v2.1.300 が最新。v2.1.301 以降のリリースは未確認）

## 🛠 GitHub の動き

- **[Sentry が XcodeBuildMCP を買収・リポジトリを getsentry/XcodeBuildMCP へ移管（2026-02-10）](https://blog.sentry.io/sentry-acquires-xcodebuildmcp/)** — Claude Code × iOS 開発の主力 MCP サーバーである XcodeBuildMCP（旧: cameroncooke/XcodeBuildMCP、4,000+ stars）を Sentry が買収し、作者の Cameron Cooke が Sentry に参画。リポジトリは `getsentry/XcodeBuildMCP` に移管され、旧 URL は自動リダイレクト。MIT ライセンスは維持、無料・OSS のまま。最新バージョンは v2.7.0（2026-10 時点）で、79 の追加ツール・11 の UI Automation ツールを含む。インストールは `claude mcp add XcodeBuildMCP -- npx -y xcodebuildmcp@latest mcp`（Node.js 不要の Homebrew インストールも選択可）。macOS 14.5+、Xcode 16.x+ が動作要件。

## 📝 日本語コミュニティ

- **[Claude Code × Swift / iOS 完全ガイド 2026（日本語版）— CLAUDE.md テンプレート・XcodeBuildMCP 設定・SwiftUI ワークフロー（The Prompt Shelf）](https://thepromptshelf.dev/blog/claude-code-swift-ios-xcode-guide-2026-ja)** — iOS 開発に特化した Claude Code セットアップを網羅した日本語ガイド。「Claude Code はターミナルで動く、Xcode は GUI ファースト」というギャップを前提に、iOS 17+ / Swift 6.0 / SwiftUI / MVVM + @Observable を対象とした CLAUDE.md テンプレートを公開。XcodeBuildMCP（stdio）のセットアップ手順、プロジェクト文脈の整備方法（docs/PRD.md・docs/specs/・docs/tasks/ の構成）、SwiftUI View のレビュー指示サンプルも含む。Xcode 26.3 の純正 MCP（xcrun mcpbridge）との併用方法にも触れている。

## 🌐 海外コミュニティ / Tips

- **[Building intelligent apps for Apple platforms with Claude in the Foundation Models framework（Anthropic 公式ブログ、2026-06-08）](https://claude.com/blog/claude-for-foundation-models)** — Anthropic が Apple の Foundation Models framework 向け公式 Swift パッケージを発表。iOS 27 / macOS 15 / Xcode 27 (beta) 以降でのみ動作し、同一の `LanguageModelSession` API を使ってオンデバイスモデルと Claude API をシームレスに切り替えられる。リクエストはアプリ → Claude API へ直接送信され Apple のインフラを経由しない。開発時は Claude API キーが必要だが、App Store 配布時のエンドユーザー認証フローは別途設計が必要。iOS 27 GA に向けて変更予定とされており、現時点は beta。Claude Code でアプリを開発しつつ、そのアプリ内でも Claude を呼び出す「二重活用」が可能になるアーキテクチャ。

- **[XcodeBuildMCP と Apple 純正 Xcode MCP（xcrun mcpbridge）を組み合わせて使う（blakecrosley.com）](https://blakecrosley.com/blog/xcode-mcp-claude-code)** — XcodeBuildMCP（ヘッドレス・Xcode 不要）と Xcode 26.3 付属の純正 MCP サーバー（xcrun mcpbridge・Xcode プロセス常駐型）を並用する設計を解説。XcodeBuildMCP は `xcodebuild` CLI ベースでシミュレーター管理・テスト・LLDB デバッグに強く、mcpbridge は SwiftUI プレビューのスクリーンショット取得・リアルタイム診断・Apple ドキュメント検索（WWDC セマンティック検索）に強みを持つ。2 サーバーを役割分担させることで、ターミナルのみ CI 環境でも Xcode UI の確認が可能になる。

## 💡 今日のおすすめ実践 Tip

**XcodeBuildMCP（getsentry）＋ Xcode 純正 MCP（xcrun mcpbridge）のデュアル設定**

2 つの MCP サーバーを組み合わせると、Claude Code が「ビルド・テスト・デバッグ（XcodeBuildMCP）」と「SwiftUI プレビュー・診断・ドキュメント検索（xcrun mcpbridge）」を使い分けるようになる。

```bash
# 1. XcodeBuildMCP をプロジェクトスコープで登録（getsentry 版）
claude mcp add --scope project XcodeBuildMCP \
  -- npx -y xcodebuildmcp@latest mcp

# 2. Apple 純正 Xcode MCP を登録（Xcode 26.3 以降が必要）
#    実行中の Xcode プロセスにブリッジするため Xcode を起動しておくこと
claude mcp add --scope project --transport stdio xcode \
  -- xcrun mcpbridge
```

登録後は `.mcp.json` に書き出されるため `git commit` すればチーム全員が同じ設定を使える。

```json
// .mcp.json（自動生成される例）
{
  "mcpServers": {
    "XcodeBuildMCP": {
      "command": "npx",
      "args": ["-y", "xcodebuildmcp@latest", "mcp"]
    },
    "xcode": {
      "command": "xcrun",
      "args": ["mcpbridge"],
      "transport": "stdio"
    }
  }
}
```

**使い分けの目安:**
- ビルドエラー解析・シミュレーター起動・LLDB デバッグ → XcodeBuildMCP
- SwiftUI Preview のスクリーンショット確認・実機診断ログ → xcrun mcpbridge
- CI（GitHub Actions・Xcode Cloud）などヘッドレス環境 → XcodeBuildMCP のみ（mcpbridge は Xcode プロセスが必要なため CI 不可）
- Apple 公式ドキュメント検索・WWDC セッション参照 → xcrun mcpbridge

なお mcpbridge は Xcode 26.3 GA 版を使うこと。RC1 には `structuredContent` フィールド欠落による spec 非準拠バグがあり、一部クライアントで応答が無視される既知の問題がある。
