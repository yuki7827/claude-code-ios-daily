---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-11-08"
date: 2026-11-08 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- 該当なし（2026-11-08 時点で直近の新規リリースは確認できず。最新は前日掲載の v2.1.319 が継続。[公式 Changelog](https://code.claude.com/docs/en/changelog) を参照）

## 🛠 GitHub の動き

- 該当なし（2026-11-08 時点で直近 5 件に未掲載の新規 iOS / Xcode / Swift 関連 Issue・PR は確認できず）

## 📝 日本語コミュニティ

- 該当なし（2026-11-08 時点で、直近 5 件に未掲載の新規 Zenn / Qiita 記事は確認できず）

## 🌐 海外コミュニティ / Tips

- [How I Shipped 5 iOS Apps in 30 Days as a Solo Dev（dev.to / snake_sun）](https://dev.to/snake_sun/how-i-shipped-5-ios-apps-in-30-days-as-a-solo-dev-claude-code-fastlane-asc-api-31kd) — Claude Code + Fastlane + App Store Connect API を組み合わせ、ソロ開発者が 30 日で 5 本の iOS アプリを App Store に出荷した実践レポート。ポイントは Fastlane の `lane :beta` と ASC API キー認証の組み合わせで証明書管理を自動化し、Claude Code がコード修正からビルドコマンド実行・エラー修正ループまでを担う構成。Mac なしで Windows マシンから CI 経由で完結させた事例として注目。

- [Give Your Simulator Superpowers — Xcode 27 のエージェントスキルを Claude Code / Codex / Cursor で使う（avanderlee.com）](https://www.avanderlee.com/ai-development/using-xcode-27s-agent-skills-in-claude-codex-and-cursor/) — Xcode 27 が同梱するエージェントスキル（`swiftui-specialist` など 7 種）を Claude Code 向けにエクスポートする方法を解説。ターミナルで `xcrun agent skills export ~/.agents/skills` を実行するとマークダウン形式のスキルファイルが生成され、Claude Code がそれを参照して SwiftUI の最新 API ベストプラクティスや Device Interaction を把握できるようになる。Xcode を起動せずに外部 IDE から Xcode 27 のスキル知識を活用できる点が実用的。

- [How to Automate App Store Submissions with Claude Code Using Blitz MCP（dev.to / gentic_news）](https://dev.to/gentic_news/how-to-automate-app-store-submissions-with-claude-code-using-blitz-mcp-1423) — ネイティブ macOS アプリ「Blitz」が提供する MCP サーバー経由で、App Store Connect API の 35 以上のツールを Claude Code から直接呼び出せる仕組みを解説。TestFlight 配信から App Store 審査提出まで自然言語プロンプトで完結できるワークフロー例が紹介されている。インストールは `claude mcp add --transport stdio io.github.blitzdotdev-blitz -- npx -y @blitzdev/blitz-mcp` の 1 コマンド。Fastlane に比べてエージェント駆動のワークフローとの親和性が高いとされ、2026 年 3 月 OSS 公開（Apache 2.0）。

## 💡 今日のおすすめ実践 Tip

**`xcrun agent skills export` で Xcode 27 のスキルを Claude Code に持ち込む**

Xcode 27 には Apple が公式に用意したエージェントスキル（SwiftUI・API ガイドライン・デバイス操作など）が同梱されている。以下のコマンドで Claude Code が参照できるフォルダにエクスポートできる：

```bash
xcrun agent skills export ~/.agents/skills
```

実行後、`~/.agents/skills/` 以下にマークダウン形式のスキルファイルが生成される。Claude Code の `CLAUDE.md` から参照させるには以下のように設定する：

```markdown
# エージェントスキル
- SwiftUI ベストプラクティス: ~/.agents/skills/swiftui-specialist.md
- デバイス操作ガイド: ~/.agents/skills/device-interaction.md
```

注意点として、`device-interaction` スキルは Xcode のシミュレーター連携ツールに依存するため、Xcode 外部（Claude Code の CLI 環境）では一部機能が制限される。CLI で使える `swiftui-specialist` や `swift-concurrency` スキルから先に試すと効果を実感しやすい。エクスポートしたスキルは Xcode のアップデートで内容が変わる可能性があるため、`xcodebuild -version` で確認した上で定期的に再エクスポートを推奨する。
