---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-06-17"
date: 2026-06-17 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Claude Code v2.1.178（2026-06-15）](https://code.claude.com/docs/en/changelog) — `Tool(param:value)` 構文でパーミッションルールにパラメータマッチングが追加。`Bash(command:rm -rf*)` で破壊的シェル操作を事前拒否、`Agent(model:opus)` でOpusサブエージェントのみブロックするなど、iOS CI 環境でも細粒度の制御が可能になった。また、ネストされた `.claude/skills` ディレクトリから自動でスキルを読み込めるようになり（名前衝突時は `<dir>:<name>` 形式で区別）、モノレポ構成のiOSプロジェクトでフォルダ単位にスキルを配置できる。

- [Anthropic、6月15日のAgent SDK課金変更を当日に一時停止（The New Stack）](https://thenewstack.io/anthropic-pauses-claude-agent-sdk-subscription-change/) — 施行予定だった `claude -p`・Agent SDK・Claude Code GitHub Actionsのサブスク枠分離を、Anthropicが6月15日当日に撤回。fastlane GitHub Actions や定期自動化は引き続き通常のサブスクリプション枠のまま継続できる。「実際の利用パターンとの整合を図りながら再設計中」とのことで、新たな施行日は未定。

## 🛠 GitHub の動き

- [AvdLee/SwiftUI-Agent-Skill（v4.0.0・2026-06-16更新・★3,000以上）](https://github.com/AvdLee/SwiftUI-Agent-Skill) — SwiftLeeのAntoine van der LeeによるSwiftUI特化のAgent Skillが昨日メジャーアップデート。iOS 26+ Liquid Glass API・アニメーション・パフォーマンスカバレッジが大幅拡充された。`npx skills add https://github.com/avdlee/swiftui-agent-skill --skill swiftui-expert-skill` で導入後、「`swiftui-expert` スキルを使って現在のSwiftUIコードの状態管理を改善して」と指示するだけで動く。Instrumentsトレース解析ツール（Python）も同梱されており、パフォーマンス問題の自動診断もできる。

- [AvdLee/Swift-Testing-Agent-Skill（v1.2.0・★410）](https://github.com/AvdLee/Swift-Testing-Agent-Skill) — 同じくAntoine van der Leeによる、Swift Testing専用スキル。`@Test`・`#expect`・`#require`・パラメータ化テスト・XCTestからの段階的移行・非同期テストパターンをカバー。LLMが犯しがちな典型的ミス（XCTestのアサーション構文をそのまま書くなど）を防ぎながら、Swift 6.2以降の最新テスト作法に沿ったテストコードを生成・改善できる。

- [vabole/apple-skills（★263）](https://github.com/vabole/apple-skills) — iOS 26+/Swift 6に特化した包括的なApple開発スキルセット。SwiftUI・UIKit・Swift Testing・Swift Concurrency・HealthKit・StoreKit・MapKit・WidgetKit など主要フレームワークの最新API参照と、Liquid Glass設計ガイドライン・パフォーマンス監査・ビューリファクタリングなどのパターンガイドを収録。Paul Hudson氏、Antoine van der Lee氏らの知見を反映。Claude Code・Codex・Cursor対応。

## 📝 日本語コミュニティ

- [Claude Codeで5日でiOSアプリをApp Storeに出した全記録（Zenn / seeda_yuto）](https://zenn.dev/seeda_yuto/articles/claude-code-ios-app-5days) — 約4,000行のSwiftUIコードで構成されるiOSアプリ「VoltKeep」をClaude Code（Maxプラン）だけで5日・55コミットで開発してApp Storeに提出した実践記録。「何を任せてよいか・何を任せてはいけないか」の知見が整理されており、個人iOS開発でClaude Codeを使い始める際の指針として参考になる。

## 🌐 海外コミュニティ / Tips

- [SwiftUI Agent Skill: Build better views with AI（SwiftLee / Antoine van der Lee）](https://www.avanderlee.com/ai-development/swiftui-agent-skill-build-better-views-with-ai/) — AvdLee/SwiftUI-Agent-Skill の設計思想と実際の使い方を著者自身が解説。AIが「最新のSwiftUI APIでは最良実践を学習していない」という問題に対し、スキルファイルで意図した振る舞いに誘導する方法を具体的なコード例とともに紹介している。v4.0.0で追加されたLiquid Glass・Instrumentsトレース連携にも言及。

## 💡 今日のおすすめ実践 Tip

**「`Tool(param:value)` でiOSビルドのサブエージェント権限をきめ細かく制御する」**

v2.1.178の新しいパーミッション構文は、iOS CI環境でサブエージェントを使う際に特に威力を発揮します。ビルドとテストは許可しつつ、破壊的な副作用を起こしうるコマンドだけを拒否する設定が `.claude/settings.json` に数行で書けます。

```json
{
  "permissions": {
    "deny": [
      "Bash(command:rm -rf*)",
      "Bash(command:git push --force*)",
      "Agent(model:opus)"
    ],
    "allow": [
      "Bash(command:xcodebuild*)",
      "Bash(command:swift build*)",
      "Bash(command:xcrun simctl*)",
      "Bash(command:fastlane*)"
    ]
  }
}
```

設定の意図:
- `rm -rf` や `git push --force` など破壊的なコマンドをサブエージェントが自律実行するのを禁止
- Opusサブエージェント（コスト高）をブロックし、Sonnet ベースのサブエージェントのみ許可
- Xcodeビルド・シミュレータ・fastlane は明示的に許可

本日紹介した AvdLee/SwiftUI-Agent-Skill v4.0.0 のような外部スキルを導入した際も、スキルが呼び出すツールをこの粒度で制御できるため安心です。Agent SDK 課金変更が一時停止され「自動化コストをひとまず気にせず動かせる」状況になった今こそ、権限設計を先に整えてから本格的に自動化を展開するのがおすすめです。
