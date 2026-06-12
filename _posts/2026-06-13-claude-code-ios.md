---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-06-13"
date: 2026-06-13 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [What's New - SwiftUI（Apple Developer）](https://developer.apple.com/swiftui/whats-new/) — WWDC 2026 / Xcode 27で、クラスを保持する `@State` がマクロベースの実装になり、View のライフタイム中に1回だけ遅延初期化されるよう変更された。iOS 17以降へのバックデプロイも可能。06-12で紹介したXcode 27公式Agent Skill「`swiftui-whats-new-27`」はこの挙動変更をClaude Codeに伝える役割を持ち、既存の `@StateObject` / `@State` の初期化コードを書き換える際の判断材料になる。

- [Xcode 27 Beta Release Notes（Apple Developer）](https://developer.apple.com/documentation/xcode-release-notes/xcode-27-release-notes) — 6/8公開のXcode 27 betaで追加された「Device Hub」により、Claude(Anthropic) / Gemini / GPTいずれのコーディングエージェントもシミュレータ・実機を直接操作してplan→code→test→verifyのループを回せるようになった。06-09に紹介したAnthropic API統合と合わせ、Claude Codeから呼び出すXcode連携の検証ループがさらに実機寄りになる。

## 🛠 GitHub の動き

- [twostraws/swift-agent-skills](https://github.com/twostraws/swift-agent-skills) — Swift界で著名なPaul Hudson（Hacking with Swift）による、Claude Code / Codex / Cursor向けオープンソースSwift・Appleプラットフォーム向けagent skillsのキュレーションリポジトリ。SwiftUI・SwiftData・Swift Concurrency・テスト・アクセシビリティ・App Store対応など50以上のスキルをカテゴリ別に整理。同梱の[SwiftUI-Agent-Skill](https://github.com/twostraws/swiftui-agent-skill)（v1.1.0）は `/plugin marketplace add twostraws/SwiftUI-Agent-Skill` → `/plugin install swiftui-pro@swiftui-agent-skill` で導入でき、`/swiftui-pro` コマンドでナビゲーション・レイアウト・状態管理・非推奨APIなどLLMが書きがちな誤りを修正させられる。

- [kbelasheuski/ios-architecture-skills](https://github.com/kbelasheuski/ios-architecture-skills)（新規・2026-06-07作成） — Claude Code / Codex向けのiOSアーキテクチャ専用スキル集。MVC・MVVM・MVI・Coordinator・VIPER・Clean Architecture・TCA・RIBsなど15パターンの使い分け・フォルダ構成・トレードオフをまとめたガイドに加え、既存コードベースの監査・スコアリングや、任意のアーキテクチャ間の移行計画生成を行う「ios-architect」ルーターを提供する。

## 📝 日本語コミュニティ

- [Claude Code × Claude Design で趣味のアプリを作った話（アシアルTechブログ）](https://blog.asial.co.jp/6808/) — Claude Code（実装・設計相談）とClaude Design（ビジュアル生成）を組み合わせて趣味のiOSアプリを開発した記録。アプリアイコン作成では、Claude Codeに生成プロンプトを書かせ、それをClaude Design・ChatGPT・Geminiへ同条件で投げて結果を比較するワークフローを紹介している。

- [Xcoded & Claude AgentはSwift Assitantから何が出来るようになったのか](https://zenn.dev/yokot/articles/0355efbf2588a4)（Zenn / yokot）— XcodeのSwift Assistant（コード補完・簡易修正のみ）と、Claude Agent SDKを統合したXcode 26.3以降のClaude Agentを比較。サブエージェント・バックグラウンドタスク・プラグイン・MCP対応によって「フルのClaude Codeハーネス」がIDE内でどこまで使えるようになったかを整理している。

## 🌐 海外コミュニティ / Tips

- [Agent skills in Xcode: How to install and use them today（Hacking with Swift / Paul Hudson）](https://www.hackingwithswift.com/articles/283/how-to-install-and-use-ai-agent-skills-in-xcode) — `twostraws/swift-agent-skills` のようなオープンソースAgent SkillsをXcodeのCoding Assistant経由・Claude Code経由の両方で使えるようにする導入手順を解説。インストール前に作者とコード内容を必ず確認すべきという安全面の注意も明記されている。

## 💡 今日のおすすめ実践 Tip

**「アーキテクチャ監査 → SwiftUI規約適用」の2段階で、Xcode 27の新`@State`挙動への移行をClaude Codeに任せる**

今日紹介した2つのGitHubリポジトリは、SwiftUIコードベースの近代化を「監査」と「規約適用」に分けて任せるのに向いています。

```bash
# 1. iOSアーキテクチャ監査スキルを導入
/plugin marketplace add kbelasheuski/ios-architecture-skills

# 2. SwiftUI規約スキルを導入
/plugin marketplace add twostraws/SwiftUI-Agent-Skill
/plugin install swiftui-pro@swiftui-agent-skill
```

Claude Code への指示例:

```
1. ios-architect で MyApp の現在のアーキテクチャ（MVVM/TCAなど）を診断し、
   @State / @StateObject / ObservableObject の使われ方を含めて
   スコアリングと改善点をレポートして。

2. レポートで指摘された @State 関連の箇所について、
   /swiftui-pro でXcode 27の新しい@State実装
   （クラスの遅延初期化・1回限りの初期化、iOS 17へのバックデプロイ可）
   に沿った形へ書き換えて。

3. 書き換え後、既存のSwiftUIプレビュー・UIテストが
   壊れていないか確認して。
```

`ios-architect` による全体構造の棚卸しを先に行うことで「どこを直すべきか」を絞り込み、`/swiftui-pro` のような規約特化スキルで「どう直すべきか」をピンポイントに適用できます。Xcode 27の公式Agent Skill `swiftui-whats-new-27`（06-12紹介）をエクスポートして併用すれば、Apple自身のガイドラインとの整合性チェックも同じセッション内で行えます。
