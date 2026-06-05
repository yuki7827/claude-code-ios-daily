---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-06-06"
date: 2026-06-06 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Claude Code v2.1.163（2026-06-04）](https://code.claude.com/docs/en/changelog) — **管理設定にバージョン強制機能を追加**。`requiredMinimumVersion`/`requiredMaximumVersion` により、許可バージョン範囲外の Claude Code は起動を拒否して承認版への移行を促す。iOS チーム全員の Claude Code バージョンを CI/CD ポリシーで統一したい場合に有用。また `/plugin list`（`--enabled`/`--disabled` フィルタ付き）コマンドが追加され、インストール済みプラグインの状態確認が容易に。`Stop`/`SubagentStop` フックが `hookSpecificOutput.additionalContext` を返せるようになり、フック終了後に Claude へのフィードバックを渡して同一ターン内で再実行を促せる。Skills の `\$` エスケープ構文追加（コマンド本体でリテラルの `$` を `\$` で記述可能）。バグ修正として、`claude -p` がバックグラウンドコマンド終了後にハングする問題、`~/Desktop/**` などホームディレクトリ経由パスで deny ルールが機能しなかった問題も解消。

- [Claude Code v2.1.165（2026-06-05）](https://code.claude.com/docs/en/changelog) — バグ修正と信頼性向上のみのパッチリリース（ユーザー向け機能変更なし）。

## 🛠 GitHub の動き

- [conorluddy/xclaude-plugin（★171）](https://github.com/conorluddy/xclaude-plugin) — iOS 開発向けの Claude Code プラグイン。8 種のワークフロー別 MCP サーバー（`xc-build`・`xc-launch`・`xc-test`・`xc-ui-automation`・`xc-debug`・`xc-code-assist`・`xc-setup`・`xc-maintenance`）を提供し、現在の作業に必要な MCP だけを有効化することでトークン消費を従来比最大 87% 削減。各 MCP は 600〜3500 トークンの小さなフットプリントに設計されており、xcodebuild の長大なログを構造化 JSON に変換して Claude に渡す。XcodeBuildMCP（包括的 82 ツール）と対になる「ミニマル・ワークフロー特化型」として使い分けられる。`conorluddy/xc-mcp`（同作者の軽量 Xcode CLI ラッパー）と組み合わせると `--mini` パラメータでビルドのみを極小コンテキストで実行可能。

- [keskinonur/claude-code-ios-dev-guide](https://github.com/keskinonur/claude-code-ios-dev-guide) — Claude Code CLI × Swift/SwiftUI iOS 開発の包括的セットアップガイド（GitHub リポジトリ形式）。PRD 駆動ワークフロー・`/effort ultrathink`・Planning Mode を組み合わせた開発フロー、Swift 6 strict concurrency や `@Observable` 優先などのベストプラクティス、Apple Developer Docs・WWDC セッション参照の組み込み方を整理。WWDC 2026（6/8〜）前後に iOS 27 Beta 対応ワークフローを整備したい開発者の出発点として有用。

## 📝 日本語コミュニティ

- [Xcode MCP×Claude Codeプラグインで、iOSビルドを自動化する](https://zenn.dev/kyoichi/articles/claude-code-plugin-xcode-mcp-hybrid)（Zenn / kyoichi）— Apple 公式 MCP（`xcrun mcpbridge`）と XcodeBuildMCP の 2 サーバー構成を Claude Code プラグインとして統合し、ビルド・テスト・SwiftUI Preview を全自動化する手順を解説。IDE 統合（ドキュメント検索・Preview）と CLI ヘッドレス操作（シミュレーター・UI オートメーション）の役割分担を具体例付きで整理。

- [Claude Codeスキルで「AIっぽくない」UIを超える：Apple風デザインを簡単に生成する方法](https://zenn.dev/tmasuyama1114/articles/apple_design_skills)（Zenn / tmasuyama1114）— Claude Code スキルに Apple HIG・Liquid Glass ガイドラインを組み込むことで、Claude が生成する SwiftUI コードを「AI っぽい没個性 UI」から「Apple ネイティブ品質」に引き上げる手法を紹介。カスタムスキルへの Apple デザインリファレンスの組み込み方が解説されている。

## 🌐 海外コミュニティ / Tips

- **明後日 WWDC 2026 Keynote 開幕**（6/8 月・日本時間 6/9 午前 2 時） — iOS 27 / macOS 27 Developer Beta が即日公開予定。Foundation Models フレームワーク拡充・新 Siri Extensions・SwiftUI の新 API が Claude Code との組み合わせ開発に直接影響する。Beta 公開後すぐに試せるよう、XcodeBuildMCP / xclaude-plugin・Swift スキルの環境を今日中に整えておくとスムーズ。

- [Two MCP Servers Made Claude Code an iOS Build System（blakecrosley.com）](https://blakecrosley.com/blog/xcode-mcp-claude-code) — Apple MCP（`xcrun mcpbridge`）と XcodeBuildMCP の 2 サーバー構成で Claude Code を iOS の完全ビルドシステムにする手法を解説。Apple MCP はドキュメント検索・Preview 取得、XcodeBuildMCP はシミュレーター起動・テスト実行・UI 操作を担当する役割分担の実装例が参考になる。

## 💡 今日のおすすめ実践 Tip

**`requiredMinimumVersion` でチームの Claude Code バージョンを固定し、WWDC Beta 環境の混乱を防ぐ**

来週から iOS 27 Developer Beta が届くと、Claude Code のバージョン差異（特に XcodeBuildMCP や Apple MCP との互換性）が問題になりやすくなります。今のうちにチームの `.claude/settings.json`（または企業管理設定）に以下を追加しておくと、古いバージョンの Claude Code が誤って使われるのを防げます。

```json
{
  "requiredMinimumVersion": "2.1.163"
}
```

iOS CI/CD（GitHub Actions・Xcode Cloud）側でも同様に設定することで、パイプライン上の Claude Code が想定外のバージョンで動くケースを排除できます。バージョン範囲外でセッションを開始しようとすると、Claude Code が起動を拒否して承認版へのアップグレードを案内するため、チームメンバーへの周知コストも下がります。

Beta 環境では Swift API の破壊的変更が発生しやすいため、`requiredMaximumVersion` を省略して上限を設けない形にしておくと、迅速なアップデートにも対応できます。

```json
{
  "requiredMinimumVersion": "2.1.163"
}
```

あわせて `SubagentStop` フックの `additionalContext` を活用し、ビルドが失敗したときにエラーの要約を Claude に返してリトライを促す仕組みも組み込んでおくと、WWDC Beta 期間の実験的なコード変更サイクルがより速くなります。
