---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-06-22"
date: 2026-06-22 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- 該当なし（本日時点の最新版は v2.1.185 / 2026-06-20）

## 🛠 GitHub の動き

- [superagents-lab/xcode27-skills（★103）](https://github.com/superagents-lab/xcode27-skills) — Xcode 27 に同梱された Apple 公式の Agent Skills 7 本（swiftui-specialist・swiftui-whats-new-27・uikit-app-modernization・test-modernizer・c-bounds-safety・audit-xcode-security-settings・device-interaction）を GitHub 経由で Claude Code に一括導入できるリポ。`npx skills add superagents-lab/xcode27-skills` 1 コマンドで取り込み完了。Apple エンジニアが書いた最新 SwiftUI ベストプラクティスを Claude に読み込ませることでハルシネーション低減が期待できる。「著作権はすべて Apple に帰属」と明記した再配布パッケージ。

- [twostraws/swift-agent-skills（★2.1k）](https://github.com/twostraws/swift-agent-skills) — Paul Hudson（Hacking with Swift）がキュレーションした Swift・Apple プラットフォーム開発向け AI Agent Skills 集。SwiftUI・SwiftData・Swift Concurrency・Swift Testing・App Intents・Accessibility・Architecture・Security など 14+ カテゴリのスキルを収録し、Claude Code・Cursor・Windsurf に対応。同氏の記事（[Hacking with Swift](https://www.hackingwithswift.com/articles/283/how-to-install-and-use-ai-agent-skills-in-xcode)）と組み合わせて読むと各スキルの使いどころが把握しやすい。

- [conorluddy/xclaude-plugin（★171）](https://github.com/conorluddy/xclaude-plugin) — iOS 開発専用の Claude Code プラグイン。xc-build（600 トークン）・xc-launch（400 トークン）・xc-interact（900 トークン）・xc-ai-assist・xc-setup・xc-testing・xc-meta・xc-all の 8 つのモジュラー MCP を必要に応じて組み合わせる設計で、最大 87% のトークン削減を実現。ビルドエラーを構造化 JSON として返す xc-build は Raw xcodebuild ログ比で大幅なコンテキスト節約になる。同作者の xc-mcp（29 ツール一体型）よりもモジュール分割が細かく、工程に応じた MCP 切り替えが可能。`/plugin marketplace add conorluddy/xclaude-plugin` で導入可。

## 📝 日本語コミュニティ

- [Xcode 26.3エージェント型コーディング入門 — Claude・Codex・MCPで開発を自動化（Qiita / kai_kou）](https://qiita.com/kai_kou/items/1b24ad62dde4c02ae4f0) — Xcode 26.3 のエージェントコーディング機能（Claude Agent SDK・Codex・MCP 連携）を使って iOS 開発を自動化するための入門記事。mcpbridge 経由の Apple 公式 MCP・XcodeBuildMCP との設定手順と、Claude に SwiftUI コンポーネントを自然言語で指示して自律的にビルド・修正させるフローを日本語で丁寧に解説している。

## 🌐 海外コミュニティ / Tips

- [Using Xcode 27's Agent Skills in Claude, Codex, and Cursor（SwiftLee / Antoine van der Lee）](https://www.avanderlee.com/ai-development/using-xcode-27s-agent-skills-in-claude-codex-and-cursor/) — Xcode 27 の `xcrun agent skills export` で取り出した 7 スキルを Claude Code に取り込む手順と使い分け基準を解説。「知識系」（swiftui-specialist・swiftui-whats-new-27・test-modernizer 等）はファイル読み書きのみで Claude Code での利用に適し、「デバイス操作系」（device-interaction）は Xcode 上での実行が前提、という整理が実践的。

- [Agent skills in Xcode: How to install and use them today（Hacking with Swift / Paul Hudson）](https://www.hackingwithswift.com/articles/283/how-to-install-and-use-ai-agent-skills-in-xcode) — Paul Hudson による Xcode Agent Skills のインストール・活用解説。`/swiftui-specialist` などスキルを名前で直接呼び出す方法・自動トリガーの仕組み・`/clear` でリロードが必要なタイミングを丁寧に説明。Apple 公式スキルと `twostraws/swift-agent-skills` を組み合わせた構成例も紹介。

## 💡 今日のおすすめ実践 Tip

**「Apple 公式 swiftui-specialist + twostraws/swift-agent-skills で、SwiftUI コードのハルシネーションを二重に防ぐ」**

Xcode 27 に同梱された `swiftui-specialist` は Apple エンジニアが書いた最新の SwiftUI ベストプラクティスです。`superagents-lab/xcode27-skills` を使えば Xcode 27 がなくても Claude Code に取り込めます。さらに `twostraws/swift-agent-skills` を重ね、カテゴリ別スキルで補完することで、API のハルシネーション（存在しないメソッドの提案など）を大幅に減らせます。

```bash
# 1. Apple 公式 Xcode 27 スキルを導入
npx skills add superagents-lab/xcode27-skills

# 2. Hacking with Swift キュレーション スキルも追加
npx skills add twostraws/swift-agent-skills

# 3. Claude Code を再起動（または /clear でリロード）
```

Claude Code への指示例:

```
swiftui-specialist スキルと swift-concurrency スキルを使って
ContentView.swift と ViewModel.swift をレビューして。

確認ポイント:
1. ForEach の id が安定した値か（swiftui-specialist）
2. @MainActor の使い方が Swift 6 strict concurrency に準拠しているか
   （swift-concurrency）
3. soft-deprecated API（NavigationView 等）が残っていたら
   swiftui-whats-new-27 を参照して iOS 27 の新 API に置き換えて

修正後は XcodeBuildMCP の xc-build で構造化エラーを確認し、
エラーがなければコミットして。
```

`swiftui-specialist`（Apple 公式のベストプラクティス）と `swift-concurrency`（Swift 6 準拠ガイド）を組み合わせることで、モダンな SwiftUI × Concurrency の両面をカバーしたレビューが 1 回の指示で完結します。`xc-build`（600 トークン、conorluddy/xclaude-plugin）でビルド確認を挟むと、コンテキストを節約しながらエラー検出→自律修正のループを回せます。
