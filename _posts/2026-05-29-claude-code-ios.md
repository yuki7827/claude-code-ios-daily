---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-05-29"
date: 2026-05-29 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Claude Code v2.1.154 + Claude Opus 4.8（2026-05-28）](https://www.anthropic.com/news/claude-opus-4-8) — Claude Code の最新バージョンに Opus 4.8 が統合。SWE-bench Pro 69.2%（Opus 4.7 の 64.3% から向上）、コードの欠陥を見逃さない確率が Opus 4.7 比 4 倍改善。**Dynamic Workflows（リサーチプレビュー）** が同時解禁され、単一セッション内で数十〜数百のサブエージェントを並列起動して大規模コードベース移行を一気に完遂できる。Max / Team / Enterprise で利用可能（Enterprise は管理者有効化が必要）。Fast mode が Opus 4.8 で 2.5× 高速化かつコスト削減。**Lean system prompt** が Opus 4.8 でデフォルト化。プラグインに `defaultEnabled: false` 宣言をサポートし、必要なときだけ有効化できる仕組みが整った。

- [Claude Code v2.1.153（2026-05-28）](https://code.claude.com/docs/en/changelog) — macOS で Claude Code のバックグラウンドエージェントがシステム環境設定「プライバシーとセキュリティ」に「Claude Code」として表示されるよう改善。ステータスラインコマンドが `COLUMNS` / `LINES` 環境変数を受け取るように変更。npm グローバルインストールで自動アップデートが失敗した際に通知を表示。

## 🛠 GitHub の動き

- [getsentry/XcodeBuildMCP v2.5.2（2026-05-12）](https://github.com/getsentry/XcodeBuildMCP) — iOS 開発向け MCP サーバーがメジャーアップデート。**構造化ツール結果（structured output）** に対応し、ビルドステータス・ログパス・テスト失敗箇所・バンドル ID などをエージェントがテキストスクレイピングなしに直接参照可能に。シミュレータの**ソフトウェアキーボード切り替えツール**（`toggle_software_keyboard` / `toggle_connect_hardware_keyboard`）が追加され、テキスト入力 UI の自動検証が容易になった。デフォルトシミュレータが iPhone 16 → iPhone 17 に変更。AXe 1.5.2 へ更新。

- [Trentobobbi/uikit-mastery-playbook（2026-05-24）](https://github.com/Trentobobbi/uikit-mastery-playbook) — UIKit 向け Claude Code スキルプレイブック（2026 ベストプラクティス特化）。iOS 開発の高パフォーマンス Swift コード生成・レビュー・リファクタリングを想定したエージェントスキル集で、Claude Code / Cursor 両対応。topics に `agent-skills` `uikit` `ios-development` `swift` を持つ。

- [anukulgosavi1-coder/sqlitedata-extensions-playbook（2026-05-24）](https://github.com/anukulgosavi1-coder/sqlitedata-extensions-playbook) — Swift の永続化ライブラリ選択を支援する Claude Code スキルキット。GRDB・SwiftData・CloudKit 連携・マイグレーション設計を網羅し、`@Table` デコレーターによる宣言型スキーマ定義や CloudKit SyncEngine パターンを Claude Code で実装する際の参照スキルとして設計。topics に `grdb` `swiftdata` `cloudkit` `ios` `swiftui` を持つ。

## 📝 日本語コミュニティ

- [Claude Code「使い放題」は終わるのか？6月改定の全容と開発者がやるべきこと](https://zenn.dev/sanpi34/articles/claude-code-pricing-shift-2026) (sanpi34, Zenn) — 2026年6月15日に施行される料金体系変更の解説記事。インタラクティブ利用（サブスク枠）とプログラム的利用（Agent SDK クレジット枠）が分離され、fastlane + `claude -p` による CI/CD 自動化や GitHub Actions で Claude Code を夜間バッチ実行している iOS チームは追加課金対象になる可能性がある。6月以降の試算方法と対策を整理。

- 該当なし — 本日時点で他に iOS × Claude Code の新規記事は確認できず。

## 🌐 海外コミュニティ / Tips

- [Anthropic upgrades Claude with new Opus 4.8 model, here's what's new](https://9to5mac.com/2026/05/28/anthropic-upgrades-claude-with-new-opus-4-8-model-heres-whats-new/) (9to5Mac, 2026-05-28) — Apple 開発者向けメディア 9to5Mac が Opus 4.8 を解説。Xcode 統合環境で利用できる Claude が Opus 4.8 にアップグレードされ、コード品質チェックの精度向上とコスト効率の両立を評価。

- [Anthropic releases Opus 4.8 with new 'dynamic workflow' tool](https://techcrunch.com/2026/05/28/anthropic-releases-opus-4-8-with-new-dynamic-workflow-tool/) (TechCrunch, 2026-05-28) — Dynamic Workflows の仕組みを解説。「何十万行ものコードベース全体のマイグレーションをキックオフからマージまで自律実行する」ユースケースが紹介されており、UIKit → SwiftUI や Swift 5.x → 6.x の大規模移行に直結する。単一セッション内でサブエージェントが互いの出力を検証しながら反復するため、矛盾する変更が混入しにくい設計。

## 💡 今日のおすすめ実践 Tip

**Dynamic Workflows で UIKit → SwiftUI の大規模移行を並列サブエージェントに分担させる**

Opus 4.8 と Dynamic Workflows（Max / Team / Enterprise でリサーチプレビュー）を組み合わせると、画面単位でサブエージェントを分割し並列で移行作業を進めることができます。既存の Xcode テストスイートを「正解の基準」として使うのがポイントです。

```markdown
# Dynamic Workflows を使った UIKit → SwiftUI 移行の指示例

以下の手順で UIKit → SwiftUI の移行を完遂してください。
動的ワークフローとして画面ごとにサブエージェントを立ち上げ、
それぞれが独立して作業し、テストが通ることを確認したうえでマージしてください。

## 制約
- 移行対象: Sources/Features/Profile, Sources/Features/Settings（各担当サブエージェントが並列処理）
- 正解の基準: xcodebuild test -scheme MyApp がすべてグリーンになること
- .pbxproj の直接編集は禁止（XcodeWrite / XcodeRM ツールのみ使用すること）
- UIHostingController ラッパーを中間状態として使い、UIKit ナビゲーションを保持
```

Dynamic Workflows では各サブエージェントの出力を Claude が検証・統合するため、サブエージェント同士が矛盾する変更を入れるリスクを自動的に低減します。iOS の場合は XcodeBuildMCP の構造化結果（v2.5 新機能）と組み合わせることで、ビルドエラーをサブエージェントが自律修正してから統合フェーズに進む流れを自然に組めます。
