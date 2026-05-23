---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-05-24"
date: 2026-05-24 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Claude Code v2.1.149 (May 22)](https://github.com/anthropics/claude-code/releases) — iOS 開発者に直接影響するコスト可視化と操作性改善。**`/usage` がカテゴリ別内訳に対応**し、スキル・サブエージェント・プラグイン・MCP サーバーごとの使用量を個別に確認できるようになった。XcodeBuildMCP や xcrun mcpbridge のツール呼び出しがどれだけトークンを消費しているかをリアルタイムに把握でき、6 月以降の Credit Pool 移行後のコスト管理に役立つ。**`/diff` ビューがキーボードスクロール対応**（矢印キー・j/k・PgUp/PgDn・Space・Home/End）。Markdown 出力が GFM のタスクリストチェックボックス（`- [ ] todo` / `- [x] done`）を正しくレンダリングするようになり、テスト項目一覧の表示が改善。Enterprise 向けに `allowAllClaudeAiMcps` の managed setting が追加。PowerShell の `cd` 組み込み関数を悪用した権限バイパスと git worktree サンドボックスの allowlist 抜け穴を修正するセキュリティ修正も含む。

- [Claude Code v2.1.150 (May 23)](https://github.com/anthropics/claude-code/releases) — 内部インフラ改善のみ。ユーザー向けの変更はなし。

## 🛠 GitHub の動き

- [199-biotechnologies/swiftui-claude-skills](https://github.com/199-biotechnologies/swiftui-claude-skills) — iOS 26 / macOS Tahoe 向けの本番品質 Claude Code スキル集（8 スキル）。**AI スウォーム検証済み**（Claude Opus 4.6・GPT-5.4 Codex・Gemini 3.1 Pro の 3 モデルで相互に誤りを指摘し合う方式）で、他リソースで見られるハルシネーション API（存在しない Liquid Glass バリアント等）を排除している。カバー範囲は Liquid Glass 全バリアント（`glassEffect`・`GlassEffectContainer`・morphing・sheet）・Spring/PhaseAnimator/KeyframeAnimator・NavigationStack パターン・SwiftData マイグレーション・**Foundation Models オンデバイス AI**（`LanguageModelSession`・構造化出力・Tool プロトコル）・Swift 6.2 Actor 分離。XcodeBuildMCP と apple-docs-mcp を前提としたセットアップ手順付き。Xcode 26.3 RC（2026年3月時点最新）で検証済み。

- [haider-nawaz/liquid-glass-skill](https://github.com/haider-nawaz/liquid-glass-skill) — iOS 26 Liquid Glass への既存アプリ移行専用の Claude Code スキル。新規実装ではなく**既存コードの移行（5 フェーズ方式）**に特化しており、`.glassEffect()`・`.backgroundExtensionEffect()`・`.buttonStyle(.glass)`・`GlassEffectContainer` の実装パターンと既知の問題／解決済みの回避策を文書化。iOS・iPadOS・macOS・watchOS・tvOS・visionOS のクロスプラットフォーム対応。Claude Code プラグインマーケットプレイスから直接インストール可能で、ガラスデザインの話題が出ると自動アクティベートする仕組み。

## 📝 日本語コミュニティ

- [iPhoneだけでiOSアプリ開発するワークフロー](https://zenn.dev/oikon/articles/dev-from-mobile) (oikon, Zenn) — iPhone 単体で iOS アプリを開発しリリースするワークフローの実録。Claude Code on the Web をスマートフォンのブラウザから操作し、GitHub Actions でビルド・TestFlight 配信まで完結させる構成を解説。Mac を持たない状況での iOS 開発を成立させるために何が必要で何が難しかったかを整理しており、リモートセッションで Claude Code を動かす際の制約と工夫が具体的にまとめられている。

- [個人開発で使ってる Claude Code の自作スキル](https://zenn.dev/hiraly/articles/b2d57af077ece1) (hiraly, Zenn) — 個人 iOS 開発者が実務で作成・運用している Claude Code 自作スキルを紹介。Swift/SwiftUI ネイティブ開発に特化したスキル（コードレビュー・リファクタリング・テスト生成）の設計方針と、公開スキルではカバーしきれない自プロジェクト固有の命名規則や API 設計方針を組み込む方法を解説。スキルの粒度をどう決めるかについての実践的な判断基準が参考になる。

## 🌐 海外コミュニティ / Tips

- [vabole/apple-skills](https://github.com/vabole/apple-skills) — Apple 開発向けエージェントスキル集（236 stars・68 commits）。「LLM の学習データには旧来の Apple API がすでに大量に含まれている」という前提のもと、**レガシーパターンを意図的に排除し iOS 26+ / Swift 6 のモダン API のみをカバーする**方針が特徴。SwiftUI・UIKit・SwiftData・Core Data・HealthKit・EventKit・Photos・通知・App Intents・ウィジェット・Liquid Glass・`@Observable` マクロ・`NavigationStack`・Swift Testing・async/await をカバー。Claude Code マーケットプレイス・Codex・Cursor からインストール可能。MIT ライセンス。

- [From Red Builds to Release: How I Ship iOS Features 2× Faster with Claude in Xcode 26](https://alirezarezvani.medium.com/from-red-builds-to-release-how-i-ship-ios-features-2-faster-with-claude-in-xcode-26-204265c7919c) (Reza Rezvani, Medium) — Xcode 26 内の Claude エージェントを活用し iOS 機能のリリースサイクルを 2 倍速にしたワークフロー実録。ビルドエラーの自律修正・SwiftUI Preview によるビジュアル確認・TestFlight へのデプロイまでを Claude エージェントが担う構成で、人間がレビューすべきポイントとエージェントに委ねるポイントの線引きを実体験から整理している。

## 💡 今日のおすすめ実践 Tip

**`/usage` で XcodeBuildMCP のトークンコストを把握してから 6 月の Credit Pool 移行に備える**

v2.1.149 で `/usage` がカテゴリ別内訳（スキル・サブエージェント・プラグイン・MCP サーバー単位）を表示するようになりました。XcodeBuildMCP の 82 ツールはビルド・テスト・シミュレータ操作ごとに毎回ツールコールが発生するため、意外と MCP サーバー枠のコストが大きくなりがちです。

```bash
# セッション終了前に /usage を実行してカテゴリ別内訳を確認
/usage

# 出力例（イメージ）:
# MCP Servers:
#   xcodebuildmcp   ████████  42%
#   xcrun-mcpbridge ████      18%
# Skills:            ██        8%
# Subagents:         ████      20%
# Direct tools:      ███       12%
```

把握したうえでコスト最適化する場合は以下が有効です：

1. **ビルド前にスキルで要件整理** — XcodeBuildMCP を呼ぶ前にスキルで設計を固めると、ビルド→エラー修正ループの回数を減らせる
2. **MCP ツール数を絞る** — `haider-nawaz/liquid-glass-skill` などドメイン特化スキルは MCP サーバーを呼ばずに回答するため、Liquid Glass 実装中は MCP 依存を減らせる
3. **6 月 15 日以降は `claude -p` 経由の CI 呼び出しが Credit Pool 対象** — ローカル対話セッションとは別枠になるため、fastlane から `claude -p` を呼ぶスクリプトは事前に使用量を見積もっておく
