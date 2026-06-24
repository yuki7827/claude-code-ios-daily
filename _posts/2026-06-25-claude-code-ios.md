---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-06-25"
date: 2026-06-25 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Claude Code v2.1.187（2026-06-23）](https://code.claude.com/docs/en/changelog) — iOS 開発・CI 環境に影響する 3 つの改善を含む。①`sandbox.credentials` 設定を追加：サンドボックス化コマンドが認証情報ファイルや秘密環境変数を読み取れないよう制御できる（Apple ID・App Store Connect API キーなどの secrets 漏洩防止に有用）。②リモート MCP ツールが無応答のまま 5 分ハングした場合にエラーで自動中断するよう修正（`CLAUDE_CODE_MCP_TOOL_IDLE_TIMEOUT` で上書き可能）。XcodeBuildMCP セッションで発生していたデッドロック問題が解消される。③org 管理者が設定したモデル制限が `--model`・`/model`・`ANTHROPIC_MODEL` に反映されるようになり、チームで使用モデルを統一管理できる。

- [Claude Code v2.1.190（2026-06-24）](https://code.claude.com/docs/en/changelog) — バグ修正と安定性改善のみ。機能追加なし。

## 🛠 GitHub の動き

- [kylehughes/apple-platform-build-tools-claude-code-plugin](https://github.com/kylehughes/apple-platform-build-tools-claude-code-plugin) — Apple プラットフォーム向けビルド・テスト・アーカイブを Claude Code から操作できる新プラグイン。`building-apple-platform-products` Agent Skill（xcrun / xcodebuild / simctl / devicectl / SwiftPM のリファレンスドキュメント）と `builder` Subagent（スキームを自動検出・シミュレータ管理・ビルド実行・詳細ログをサマリーに変換してメインコンテキストを保護）の 2 層構成。iOS / macOS / tvOS / watchOS / visionOS をカバーし、テストのフィルタリング・カバレッジ分析・コード署名・アーカイブ配布・CI/CD パターンも含む。XcodeBuildMCP と並べると「xcrun エコシステム全体への網羅的なドキュメント参照」が強みで、コマンド構築や失敗トラブルシューティングに向いている。

- [keskinonur/claude-code-ios-dev-guide](https://github.com/keskinonur/claude-code-ios-dev-guide) — PRD 駆動ワークフロー・拡張思考（`ultrathink` 32K トークン：アーキテクチャ設計 / `think hard` 10K トークン：中程度タスク）・プランモードを組み合わせた Swift/SwiftUI iOS 開発の包括ガイド。iOS 特化サブエージェント（iOS アーキテクト・SwiftUI スペシャリスト・コードレビュアー）、サンドボックスの段階的権限設定（読み取り専用→ドキュメント→テスト→フルコード）、CLAUDE.md を使ったプロジェクト・フィーチャー別コンテキスト管理を収録。XcodeBuildMCP との統合手順も含む。

## 📝 日本語コミュニティ

- [Xcode の Claude Agent・Codex が settings.json・config.toml でセットアップできるように（Zenn / treastrain）](https://zenn.dev/treastrain/articles/bc9435a36fe498) — Xcode 26.5 で Enterprise Configuration が追加され、Claude Agent（settings.json）・Codex（config.toml）を組織設定ファイルから一元管理できるようになった変更の速報。Amazon Bedrock・Google Vertex AI・Microsoft Foundry などの代替プロバイダーへの切り替えも設定ファイルで可能。大規模チームでの iOS 開発環境標準化に直結する変更。

- [iOSアプリ開発に役立つAgentic Coding集（Zenn / mtfum）](https://zenn.dev/mtfum/articles/ios_agentic_coding) — iOS アプリ開発で実際に使える Agentic Coding ツール・手法をまとめた記事。XcodeBuildMCP・Agent Skills・サブエージェント構成など実務で活用できるものを体系的に整理している。

## 🌐 海外コミュニティ / Tips

- [claude-code-ios-dev-setup（ApptitudeLabs）](https://github.com/ApptitudeLabs/claude-code-ios-dev-setup) — iOS エンジニア専用の Claude Code フルセットアップガイド。「汎用 AI ガイドに iOS を添えた内容ではなく、iOS エンジニアが Claude Code をフル活用するための実戦的ガイド」を標榜。50+ の iOS 特化スキル（Swift Concurrency・SwiftUI・Core Data・ビルド最適化）、XcodeBuildMCP と Apple 純正 mcpbridge の並用構成、PRD 駆動テンプレート、トークン最適化戦略、チーム共有用 `.mcp.json` 設定を網羅。

## 💡 今日のおすすめ実践 Tip

**「`sandbox.credentials` と MCP タイムアウト設定で iOS CI の secrets 漏洩・デッドロックを同時に防ぐ」**

v2.1.187 の 2 つの改善を組み合わせると、iOS CI 環境の安全性と安定性を同時に高められます。

**1. `sandbox.credentials` で secrets をサンドボックス内コマンドから隔離**

`.claude/settings.json` に設定を追加することで、Claude Code がサンドボックス内で実行するシェルコマンドが `~/.netrc`・`~/.ssh`・`APPLE_ID`・`ASC_API_KEY` などを読み取れなくなります。

```json
{
  "sandbox": {
    "credentials": false
  }
}
```

これにより「Claude Code が Fastlane レーンを呼び出す際に誤って API キーを含むコマンドを外部に漏洩させる」というリスクを構造的に防げます。fastlane / App Store Connect へのアクセス権は fastlane プロセス自身が持ち、Claude Code のサンドボックスには渡らない構成にできます。

**2. `CLAUDE_CODE_MCP_TOOL_IDLE_TIMEOUT` で XcodeBuildMCP のデッドロックを防ぐ**

v2.1.187 以前は MCP ツールが無応答のまま 5 分ハングすると CI が永遠に待ち続けるケースがありました。修正後はデフォルト 5 分でエラーとなりますが、`CLAUDE_CODE_MCP_TOOL_IDLE_TIMEOUT` で適切な値に調整できます。

```bash
# CI 実行前にタイムアウトを設定（例：ビルドが 3 分以上応答しなければ中断）
export CLAUDE_CODE_MCP_TOOL_IDLE_TIMEOUT=180
claude -p "XcodeBuildMCP を使って MyApp-iOS スキームをビルドして"
```

両方を組み合わせると「secrets を守りながら、デッドロックなく XcodeBuildMCP を自律実行する」iOS CI が実現できます。今日紹介した `kylehughes/apple-platform-build-tools-claude-code-plugin` の `builder` Subagent と組み合わせると、ビルドログのコンテキスト汚染も防ぎつつ 3 点を同時にクリアできます。
