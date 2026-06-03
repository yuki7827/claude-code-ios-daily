---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-06-04"
date: 2026-06-04 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Claude Code v2.1.161（2026-06-02）](https://github.com/anthropics/claude-code/releases/tag/v2.1.161) — 並行ツール呼び出しのエラー処理が改善：`Bash` コマンドが失敗しても同バッチの他のツール呼び出しがキャンセルされなくなった。`claude agents` が `done/total` 形式の進捗を表示するようになり、マルチエージェントビルドの状況が把握しやすく。`OTEL_RESOURCE_ATTRIBUTES` の値がメトリクスのラベルとして付与されるようになり、チーム・リポ単位でのトークン消費追跡が容易に。バックグラウンドセッション・worktree・MCP サーバー周辺の各種バグも修正。

- **[2026-06-15 課金変更（重要）](https://www.buildthisnow.com/blog/guide/mechanics/claude-billing-change-june-2026)** — 6 月 15 日から、インタラクティブな Claude Code（ターミナル・IDE）と、プログラム的使用（`claude -p`・GitHub Actions・Agent SDK・サードパーティエージェント）が別々のクレジットプールに分離。fastlane や Xcode Cloud などの iOS CI/CD パイプラインで `claude -p` を呼び出している場合は上限額と使用量を事前に確認すること。CI 共有認証では複数ユーザーのクレジットは合算されないため、共有 CI は Platform API（従量課金）への移行が Anthropic 公式推奨。

## 🛠 GitHub の動き

- [[BUG] Remote Control: AskUserQuestion shows 'awaiting input' on mobile — Issue #33625（CLOSED 2026-06-03）](https://github.com/anthropics/claude-code/issues/33625) — iOS デバイス（Claude モバイルアプリ）から Remote Control でセッションを監視中に `AskUserQuestion` が発火すると UI が一切表示されない問題が解決。iOS から Claude Code セッションをリモート監視しながら承認操作を行う運用が安定して使えるようになった。

- [Xopoko/build-swift-apps（★18・2026-06-03 更新）](https://github.com/Xopoko/build-swift-apps) — iOS シミュレータデバッグ・SwiftUI アーキテクチャ・Xcode ビルド最適化・App Store Connect ワークフローなど 59 の専門スキルを収録したマルチエージェント対応スキルパック。Codex・Claude Code・Cursor いずれにも対応し、全 Apple プラットフォーム（iOS / macOS / watchOS / tvOS / visionOS）をカバー。

- [Silbercue/SilbercueSwift（★4・2026-05-29 更新）](https://github.com/Silbercue/SilbercueSwift) — 「最速・最完全な iOS 開発向け MCP サーバー」を謳うネイティブ Swift 単体バイナリ。58 ツールを提供し、スクリーンショット取得が **約 15ms**（XcodeBuildMCP 比 75 倍高速）、View Hierarchy クエリが **約 5ms**（同 50 倍高速）。Node.js・Appium 不要で動作し、`xcresult` 形式でのテスト失敗解析も構造化 JSON で返す。

## 📝 日本語コミュニティ

- [Claude Code「使い放題」は終わるのか？6月改定の全容と開発者がやるべきこと](https://zenn.dev/sanpi34/articles/claude-code-pricing-shift-2026)（Zenn / sanpi34）— 6/15 課金分離の全体像を日本語で整理した記事。Max 5x・Max 20x のクレジット量の違い・CI 共有ユーザーがはまりやすい罠・「今すぐやること」チェックリストを網羅。iOS CI/CD（GitHub Actions / Xcode Cloud + `claude -p`）を組んでいる開発者が読んでおくべき一本。

## 🌐 海外コミュニティ / Tips

- [Claude Code Billing Change June 15, 2026（Build This Now）](https://www.buildthisnow.com/blog/guide/mechanics/claude-billing-change-june-2026) — 2 プール分離の仕組みをコードとフローチャートで解説。`claude -p`・GitHub Actions・Agent SDK それぞれが新クレジットプールを消費する理由と、消費量を事前に見積もるためのログの読み方を紹介。

- [Claude June 2026 Pricing Changes: Indie Dev Survival Guide（alexcloudstar.com）](https://www.alexcloudstar.com/blog/claude-june-2026-pricing-survival-guide/) — 個人・小規模チーム向けの生存戦略。スポット的な CI ビルドには Platform API、日常的なインタラクティブ開発には Max サブスクの使い分けや、`claude -p` 呼び出しを削減するための fastlane タスクの見直しポイントを具体的に提示。

## 💡 今日のおすすめ実践 Tip

**2026-06-15 前に iOS CI/CD パイプラインの `claude -p` 呼び出しを棚卸しする**

6/15 以降、`claude -p`・GitHub Actions の Claude Code integration・`claude-agent-sdk` 経由の呼び出しは Max サブスクの別クレジットプール（Pro: $20/月相当、Max 20x: $200/月相当）から消費されます。iOS プロジェクトでありがちな Claude 呼び出し箇所を確認しておきましょう。

チェックリスト：

```
□ .github/workflows/ 内の claude -p / @anthropic-ai/claude-code 呼び出し
□ Makefile / fastlane / Dangerfile 内の claude コマンド呼び出し
□ Xcode Cloud の postCloneScript / postXcodebuildScript での呼び出し
□ .claude/hooks/ 内の自動実行スクリプト
□ @anthropic-ai/claude-agent-sdk への参照（@anthropic-ai/claude-code から改名）
```

対応方針：

- **低頻度 CI**（週 1 回の snapshot テスト等）→ 新クレジットで収まる場合が多い。6/15 前後 2 週間で実消費量をモニタリング
- **高頻度 CI**（PR ごとの自動コードレビュー等）→ Anthropic 推奨は Claude Platform 従量課金 API への移行
- **チーム共有 CI ユーザー** → 各ユーザーのクレジットは合算されないため共有 CI には Platform API が必須

6/15 以降にエラーが出た場合は `ANTHROPIC_CREDIT_LIMIT_EXCEEDED` エラーを監視し、過剰消費箇所を特定して Platform API に切り出すのが最短の対処法です。
