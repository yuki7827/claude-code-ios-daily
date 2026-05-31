---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-06-01"
date: 2026-06-01 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Claude Code v2.1.159（2026-05-31）](https://code.claude.com/docs/en/changelog) — 内部インフラ改善のみ。ユーザー向けの変更はなし。

- **`CLAUDE_CODE_OPUS_4_6_FAST_MODE_OVERRIDE` 環境変数が本日（6/1）廃止** — Opus 4.6 で fast mode を使う場合は `/model claude-opus-4-6` に切り替えたうえで `/fast on` を実行する新フローに移行済みであることを確認のこと（[changelog 参照](https://code.claude.com/docs/en/changelog)）。

- [Code with Claude Tokyo（6/10-11）](https://claude.com/code-with-claude/tokyo) — Anthropic の開発者カンファレンスが東京で 6/10（本編）・6/11（Extended）に開催。ライブストリーム配信あり。6/11 は独立系開発者・早期スタートアップ向けに Applied AI チームとのハンズオンワークショップ形式で実施。iOS 開発者の参加・視聴も歓迎。

## 🛠 GitHub の動き

- [vabole/apple-skills（★246）](https://github.com/vabole/apple-skills) — Claude Code・Codex 向けに iOS 26+ / Swift 6 の最新 API だけを提供するスキル集。`ios-liquid-glass`（Liquid Glass API リファレンス）・`ios-design-consultant`（Liquid Glass UX ガイダンス）を含む 15+ カテゴリ構成。LLM のトレーニングデータにはレガシー API が大量に入っているため、あえて最新 API のみに絞り古いパターンをエージェントに踏ませない設計が特徴。`@Observable`・Swift Concurrency・SwiftData・HealthKit 等にも対応したリファレンスを収録。

- [haider-nawaz/liquid-glass-skill](https://github.com/haider-nawaz/liquid-glass-skill) — WWDC 2025 で登場した Apple の Liquid Glass デザインシステム（iOS/iPadOS/macOS/watchOS/tvOS/visionOS 26+）への移行・実装を Claude Code に任せるスキル。5 フェーズの移行ワークフロー・`.glassEffect()` 等の正しい適用方法・既知の実装問題の解決策を収録。Apple の Landmarks サンプルアプリからの実コード例付き。

- [conorluddy/LiquidGlassReference（★299）](https://github.com/conorluddy/LiquidGlassReference) — 「Claude に Liquid Glass を作らせるときに指し示すためのドキュメント」として作られた iOS 26 Liquid Glass の Swift/SwiftUI 参照集。基礎から上級テクニック・エッジケース・ベストプラクティス・API リファレンスまで 8 章構成。スキルではなくコンテキストドキュメントとして Claude Code に直接読み込ませる使い方を想定。

## 📝 日本語コミュニティ

- [Claude Code でチケット駆動開発！Jira と MCP 連携させて簡単な iOS アプリを作ってみました](https://zenn.dev/kobashuu/articles/64080a899cb3e1)（Zenn / kobashuu）— Jira MCP を Claude Code に組み合わせ、チケットに記述した要件を優先度順に Claude が読み取りながら iOS アプリを実装する「チケット駆動開発」のレポート。タスク管理と実装のループを Claude に橋渡しさせる実践的なワークフローの例として参考になる。

## 🌐 海外コミュニティ / Tips

- [WWDC 2026（6/8-12）](https://developer.apple.com/wwdc26/) — 来週月曜（日本時間 6/9 午前 2 時）に Apple の Keynote が始まり、iOS 27 / macOS 27 の発表と同時に Developer Beta が即日リリース予定。Xcode 26.3 + Claude Agent SDK 統合のさらなるアップデートも期待される週。Claude Code ユーザーは WWDC キーノート後すぐに新 Beta を試せる環境を事前に整えておくとよい。

- [Code with Claude Tokyo 2026（6/10-11）livestream](https://claude.com/code-with-claude/tokyo) — WWDC 2026 と同週（6/8-12）に開催。Apple 最新プラットフォームの発表直後に Anthropic による Claude Code の最新デモが見られる絶好のタイミング。現地参加のほかライブストリームも配信予定。

## 💡 今日のおすすめ実践 Tip

**WWDC 2026 直前: Liquid Glass スキルで iOS 27 対応棚卸しを今週中に**

来週月曜の WWDC 2026 Keynote で iOS 27 Developer Beta が公開されると、Xcode Beta の更新とともに Liquid Glass の API 変更が来る可能性があります。事前に `vabole/apple-skills` または `haider-nawaz/liquid-glass-skill` を `.claude/skills/` に配置し、現行 iOS 26 アプリの Liquid Glass 実装を Claude Code に棚卸しさせておくと、Beta が来た瞬間に差分を把握しやすくなります。

```bash
# vabole/apple-skills をプロジェクトスキルとして配置
git clone https://github.com/vabole/apple-skills /tmp/apple-skills
cp /tmp/apple-skills/skills/* .claude/skills/
```

Claude Code への指示例:

```
現在の MyApp プロジェクトで .glassEffect() を使用している箇所を
すべてリストアップし、ios-liquid-glass スキルと照合して
iOS 26 の正しいパターンを使っているか確認してください。
```

さらに `conorluddy/LiquidGlassReference` の README をコンテキストとして追加読み込みさせると、Claude が `backgroundExtensionEffect` の適切な使いどころなどのエッジケースを正確に把握した状態で診断を行えます。WWDC Beta 後は同じプロセスを iOS 27 Beta で再実行することで、API 変更箇所を即座に洗い出せます。
