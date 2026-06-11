---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-06-12"
date: 2026-06-12 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Claude Code v2.1.172（2026-06-10）](https://github.com/anthropics/claude-code/releases/tag/v2.1.172) — **サブエージェントが自身のサブエージェントを最大5階層までスポーン可能に**。Apple Platform Build Tools プラグイン（06-11紹介）の「builder」サブエージェントのようなビルド/テスト専任エージェントが、さらに内部でログ解析専用の孫エージェントを呼び出せるようになり、メインの会話コンテキストをビルドログで汚さずに多段階のiOS診断を組める。あわせて `/plugin` コマンドにマーケットプレイス検索バーを追加、Amazon Bedrock が `~/.aws` からリージョンを自動読込。v2.1.173（06-11）はFable 5の `[1m]` サフィックス表記とWindowsサンドボックス警告のバグ修正のみ。

- [⚠️ Agent SDK / `claude -p` / Claude Code GitHub Actions が6/15からサブスク枠と分離](https://qiita.com/Rapls/items/2a8d7f055065e298078b) — Anthropicが2026年5月14日にHelp Centerで正式化した課金変更で、6月15日以降は `claude -p`（非対話モード）・Agent SDK・Claude Code GitHub Actions・Agent SDK認証を使うサードパーティアプリの利用が通常のClaudeプラン枠から切り離され、「月次クレジット」（Pro $20 / Max 5x $100 / Max 20x $200、繰越なし、要claim）に移行する。06-07分の Tip で紹介した「`claude -p` をiOSのGitHub Actions CIに組み込む」構成はまさに対象。詳細な対応は本日の「おすすめ実践 Tip」を参照。

- [Xcode 27、Apple純正の Agent Skills を7種同梱しClaude Code等にエクスポート可能に（9to5Mac）](https://9to5mac.com/2026/06/10/xcode-27-expands-agentic-coding-toolset-with-gemini-integration/) — WWDC 2026 で発表されたXcode 27は `swiftui-specialist`・`swiftui-whats-new-27`・`uikit-app-modernization`・`test-modernizer`・`audit-xcode-security-settings` など7つの公式Agent Skillsをツールチェーンに同梱。CLIコマンド一発でエクスポートして `~/.agents/` に置けば、Claude Code・Codex・Cursor・GeminiからもApple自身のSwiftUI/UIKitガイダンスをスキルとして参照できる。

## 🛠 GitHub の動き

- [rshankras/claude-code-apple-skills（新規）](https://github.com/rshankras/claude-code-apple-skills) — iOS / macOS / iPadOS / watchOS / visionOS向けのClaude Codeスキル集。プロダクト検証・コード生成（52スキル）・HIG準拠チェック・CoreML/SwiftUI/Apple Intelligence対応・App Store最適化まで「アイデア出しからストア申請」を一気通貫でカバーする。`.claude/skills/` にコピーして使う構成。

- [patrickserrano/skills（ios-swift-skills プラグイン）](https://github.com/patrickserrano/skills) — Swift/SwiftUI向けスキル集。GitHub Issue番号を渡すと実装→ビルド/テスト→クローズコミット→pushまで一気通貫で行うスキル、シミュレータでのデバッグ・ログ取得スキル、Time ProfilerをCLI経由で叩くパフォーマンス解析スキル、git履歴からApp Storeリリースノートを生成するスキルなどを収録。`claude plugin marketplace add` で導入可能。

## 📝 日本語コミュニティ

- [【6/15】Claudeのプログラム利用が別課金に。自分が影響を受けるか確認するチェックリスト](https://qiita.com/Rapls/items/2a8d7f055065e298078b)（Qiita / Rapls）— `claude -p`・GitHub Actions・スケジュール実行・サードパーティツールなど「自動化系」利用の棚卸し手順、Anthropicからのクレジットclaim操作、オーバーフロー課金設定の要否、継続的な自動化はAPIキー直利用への移行を検討、といった実務チェックリストをまとめている。iOSのCIでClaude Code GitHub Actionsを使っているチームは必読。

- [claude-codeでSwiftUIを使ってみる](https://zenn.dev/jboysan/articles/fa4d22030e7ae1)（Zenn / jboysan）— Claude Code（Maxプラン）にカレンダー画面・予定詳細画面を持つSwiftUIアプリを作らせた実践記録。保存した予定の日付がカレンダー上で青くハイライトされる挙動まで、思った以上の精度で実装できたと報告している。

## 🌐 海外コミュニティ / Tips

- [Using Xcode 27's Agent Skills in Claude, Codex, and Cursor](https://www.avanderlee.com/ai-development/using-xcode-27s-agent-skills-in-claude-codex-and-cursor/)（SwiftLee / Antoine van der Lee）— Xcode 27同梱の公式Agent Skillsを `agent` CLIでエクスポートし、Claude Codeなど他ツールから読み込む具体的な手順を解説。Apple公式の `swiftui-specialist` などのスキルをClaude Codeのセッションに取り込めば、Xcodeを起動していない端末やCI環境でもApple推奨のSwiftUI設計指針に沿った実装をさせやすくなる。

- [Claude Code Nested Subagents: 5 Levels Deep](https://claudefa.st/blog/guide/agents/nested-subagents)（claudefa.st）— v2.1.172のネストサブエージェント機能の解説記事。ビルド診断を担当するレイヤー2のサブエージェントが、サーバーログとコードベースの突き合わせが必要になった際にレイヤー3のサブエージェントを生成してgrepさせ、結果を1行に要約して返すパターンを紹介。`apple-platform-build-tools-claude-code-plugin`（06-11紹介）の「builder」サブエージェントのような既存のiOSビルド/テスト用サブエージェントに、このネスト機能を組み合わせる余地が生まれている。

## 💡 今日のおすすめ実践 Tip

**6/15のAgent SDK課金分離に備えて、iOS CIの`claude -p`利用を棚卸しする**

06-07分のTipで紹介した「`fallbackModel`を設定して`claude -p`をiOSのGitHub Actions CIに組み込む」構成は、6月15日の課金ルール変更の影響を直接受けます。施行まで3日のため、今日中に以下を確認しておくのがおすすめです。

```
1. リポジトリ内で claude -p / Claude Code GitHub Actions / Agent SDK を
   呼んでいるワークフローを棚卸し（.github/workflows/*.yml を grep）
2. Anthropicアカウントでクレジットのclaim操作を済ませる
   （Pro: $20 / Max 5x: $100 / Max 20x: $200、月またぎ繰越なし）
3. 夜間の自動Swift 6移行やDynamic Workflowsのような
   「トークン消費が激増する」自動化は、6/15以降クレジット枯渇で
   止まる可能性があるため、オーバーフロー課金の要否を判断する
4. 継続的に自動化を回すワークフローは、サブスク認証ではなく
   ANTHROPIC_API_KEY による直接API利用への切り替えも検討する
```

特に、Dynamic Workflows（06-10紹介）やネストサブエージェント（本日紹介）のように「1セッションで大量のサブエージェントを呼ぶ」機能は通常セッションの数倍のトークンを消費するため、6/15以降は月次クレジットを急速に消費する可能性が高い構成です。大規模なiOSリファクタリングタスクをCIで自動実行している場合は、クレジット残量のアラート設定や、`fallbackModel`をコスト最適化の観点で見直しておくと安全です。
