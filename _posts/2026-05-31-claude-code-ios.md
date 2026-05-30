---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-05-31"
date: 2026-05-31 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Claude Code v2.1.158（2026-05-30）](https://code.claude.com/docs/en/changelog) — **Auto mode が Amazon Bedrock・Google Vertex AI・Azure Foundry 上でも Opus 4.7/4.8 に対応**。`CLAUDE_CODE_ENABLE_AUTO_MODE=1` 環境変数で有効化でき、クラウドプロバイダー経由で Claude Code を使う iOS CI/CD 環境（Bedrock ベースの Lambda や GKE 上の fastlane パイプラインなど）でも Opus 4.8 の性能をフル活用可能に。

## 🛠 GitHub の動き

- [laxrajpurohit/swift-skills-pro（2026-05-29 作成、★5）](https://github.com/laxrajpurohit/swift-skills-pro) — Swift 6 / iOS 26 に特化した 11 スキルセット。`swiftui-pro`・`swift-concurrency-pro`・`swiftdata-pro`・`core-data-pro`・`swift-language-pro`・`swift-testing-pro`・`swift-architecture-pro`・`swift-performance-pro`・`swift-accessibility-pro`・`app-intents-pro`・`swift-security-pro` で構成。各スキルに「❌ 間違い」「✅ 正しい」の例示と検査リストを付属し、古い API（`foregroundColor`・`ObservableObject` 等）の使用を排除しながらデータ競合・メモリリークを自動検出できる設計。

- [enesbytmr/store-guideline-checker（2026-05-30 作成）](https://github.com/enesbytmr/store-guideline-checker) — App Store Review Guidelines および Google Play Developer Program Policy への適合を自動監査する Claude Code プラグイン（スキル＋エージェント）。iOS Native・Flutter・React Native・Expo に対応し、パーミッション記述・IAP 設定・プライバシーマニフェスト・Sign in with Apple 対応・Data Safety Form の整合性などを提出前にチェックする。スキルは `/store-guideline-checker:guideline-checker [パス]`、エージェントは `@agent-store-guideline-checker:guideline-auditor audit this project` で起動。

- [harshii0509/swift-skill-pack（2026-05-30 作成）](https://github.com/harshii0509/swift-skill-pack) — Codex・Claude など複数のコーディングエージェント対応の 4 スキル構成（`swift` ルーター・`swift-prototype`・`swift-patterns`・`swift-debug`）。ルータースキルが実装前に適切な方向性を選定し、プロトタイプ・アーキテクチャ・デバッグを専門スキルに振り分ける設計。`npx skills add harshii0509/swift-skill-pack` でインストール可能。

- [joelklabo/iOS Project Claude Code Setup Prompt（Gist）](https://gist.github.com/joelklabo/6df9fa603bec3478dec7efc17ea44596) — XcodeBuildMCP と Claude Code を組み合わせた iOS プロジェクト向けの包括的セットアップガイド（Gist）。プロジェクト構造の自動認識・スラッシュコマンドでのビルド/テスト/デプロイ・フックによるドキュメント更新自動化など 8 フェーズで構成。CLAUDE.md テンプレートとして活用できる。

## 📝 日本語コミュニティ

該当なし — 本日時点で新規の iOS × Claude Code 日本語記事は確認できず。

## 🌐 海外コミュニティ / Tips

- [Xcode 26.3: Use AI Agents from Cursor, Claude Code & Beyond](https://dev.to/arshtechpro/xcode-263-use-ai-agents-from-cursor-claude-code-beyond-4dmi) (dev.to) — Xcode 26.3 の MCP ネイティブ対応を解説。Claude Code CLI・Cursor・任意の MCP 対応エージェントを Xcode の 20 組み込みツールに接続できる仕組みを整理し、SwiftUI プレビュー取得・Apple ドキュメント検索・Swift REPL 実行まで`xcrun mcpbridge` 1 コマンドで接続する手順を紹介。外部エージェントから Xcode を操作したい開発者向けの入門記事。

## 💡 今日のおすすめ実践 Tip

**`store-guideline-checker` で App Review 却下を提出前に防ぐ**

App Store 審査で多い却下理由（プライバシーマニフェスト未記載・Sign in with Apple 未実装・IAP 設定ミス）を、提出前に Claude Code で自動チェックできます。`enesbytmr/store-guideline-checker` を v2.1.157 以降の `.claude/skills/` 自動ロード機能と組み合わせると、チーム全員が提出前に同じチェックリストを走らせる運用が簡単に組めます。

```bash
# プロジェクトの .claude/skills/ にスキルを配置
cp store-guideline-checker/skills/* .claude/skills/
```

起動：

```
/store-guideline-checker:guideline-checker ./MyApp
```

主な自動チェック項目（iOS 向け）：

- `PrivacyInfo.xcprivacy`（プライバシーマニフェスト）の記載が API 使用と一致しているか
- Sign in with Apple が他のサードパーティ認証と並んで実装されているか
- IAP のレシート検証が最新の StoreKit 2 API を使用しているか
- `Info.plist` のパーミッション説明文が英語以外のロケールにも対応しているか

「審査前に必ず `/store-guideline-checker` を実行すること」を CLAUDE.md のルールに明記しておくと、審査リジェクト対応の工数を削減できます。
