---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-07-03"
date: 2026-07-03 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Claude Code v2.1.198（2026-07-01）— バックグラウンドエージェントが自動コミット・push・ドラフト PR 作成まで自走](https://code.claude.com/docs/en/changelog) — iOS CI 運用に直結する大型変更。`claude agents` から起動したバックグラウンドエージェントが worktree でのコード作業を完了すると、**確認なしで自動的に commit → push → GitHub ドラフト PR 作成**まで行うようになった（以前は「確認してください」と止まっていた）。PR は draft フラグが付くため、マージは人間が判断する設計。合わせて **Notification フック**（`agent_needs_input` / `agent_completed`）が追加され、エージェントが完了または入力待ちになったタイミングで Slack 通知や macOS 通知を飛ばすスクリプトを接続できる。またネットワークの一時的な切断（`ECONNRESET` 等）でターンが中断されなくなりバックオフ付きで自動再試行するようになったため、長時間の xcodebuild 実行中のセッション断が減少する。

## 🛠 GitHub の動き

- [conorluddy/xclaude-plugin — トークン節約に特化した iOS 専用 Claude Code プラグイン](https://github.com/conorluddy/xclaude-plugin) — 8 本のワークフロー別 MCP サーバー・24 ツールで構成された iOS 開発向けプラグイン。XcodeBuildMCP が全ツールをフラットに提供するのに対し、本プラグインは「ビルドエラー抽出」「UI オートメーション」「シミュレータ管理」などワークフロー単位でツールをモジュール化し、**xcodebuild の生ログではなく構造化 JSON** を Claude に渡すことでコンテキスト消費を大幅に削減する設計。ビルドエラーは最大 10 件まで自動パース、UI 操作はアクセシビリティツリーのクエリとタップで座標依存を排除。`/plugin marketplace add conorluddy/xclaude-plugin` で即導入可能。

## 📝 日本語コミュニティ

- [claude agents が自動 PR を作るようになったので中身を検証した（Qiita / kai_kou）](https://qiita.com/kai_kou/items/f4d7198f1a5db10a0663) — v2.1.198 の自動 PR 機能を実際に動かして内部動作を検証した記事。worktree の分離、draft PR として作られる理由、ブランチ保護ルール設定済み環境での挙動差などを実測レポート。iOS CI でブランチ保護を運用しているチームは push 権限と auto-PR の組み合わせを事前確認するよう注意喚起あり。

- [Claude Code v2.1.198：Claude in Chrome GA と自動 PR 作成のインパクト解説（Qiita / picnic）](https://qiita.com/picnic/items/333e17bf379a0e6a9952) — v2.1.198 の 2 大変更（Claude in Chrome 正式 GA・バックグラウンドエージェント自動 PR）の実務インパクトを解説。iOS 開発への応用として「`xcodebuild test` 実行 → テスト失敗を自律修正 → 自動 PR」の非同期ループが実質的に手放し運用できるようになった点を評価。

- [Claude Code v2.1.198｜背景エージェントが draft PR まで自走｜毎日 Changelog 解説（Qiita / moha0918_）](https://qiita.com/moha0918_/items/181b45d5e97df7012263) — v2.1.198 の全変更点を列挙した日次 Changelog 解説シリーズ。Notification フックの設定例（Slack webhook への curl）とサブエージェントが extended thinking 設定を継承するようになった点も解説。

## 🌐 海外コミュニティ / Tips

- [Agentic Coding in Xcode 26.3 with Claude Code and Codex（Swiftjective-C）](https://www.swiftjectivec.com/agentic-coding-codex-claude-code-in-xcode/) — iOS 老舗ブログ Swiftjective-C が Xcode 26.3 の Agentic Coding 機能（Claude Agent SDK 統合）を詳細解説。Claude Agent SDK は Claude Code の基盤と同一で、Xcode の GUI から **SwiftUI Preview をキャプチャして視覚的にフィードバックを受けながら反復**できる点が CLI との最大の差異。IDE を離れずにサブエージェント・バックグラウンドタスク・プラグインを使えるワークフローを実例付きで紹介。

- [Agentic Coding in Xcode 26.3 Using Claude Agent for Swift & SwiftUI — Part 1〜3（Medium / Chinthaka Parana Widanalage）](https://medium.com/@chinthaka01/part-1-agentic-coding-in-xcode-26-3-with-claude-agent-for-swift-swiftui-2dda7ec7272e) — Xcode 26.3 の Claude Agent 機能を Swift / SwiftUI 開発で実際に使う手順を 3 部構成で解説したチュートリアルシリーズ。Part 1 が環境セットアップ（Claude Agent の有効化・iOS プロジェクトへの適用）、Part 2 が Xcode 内での実際の操作フロー、Part 3 が iOS 18 以降の API に絞った活用法。Xcode 26.3 を使える Apple Developer Program メンバーがまず読む一次リソース。

## 💡 今日のおすすめ実践 Tip

**「v2.1.198 の自動 PR ＋ Notification フック + xclaude-plugin で iOS PR を手放し量産する」**

v2.1.198 のバックグラウンドエージェント自動 PR と Notification フックを組み合わせると、iOS のコード改善タスクを「投げっぱなし → 完成 PR が届く」フローで量産できます。

**1. Notification フックを設定して Slack に通知**

```json
// .claude/settings.json
{
  "hooks": {
    "Notification": [
      {
        "matcher": "agent_completed",
        "hooks": [
          {
            "type": "command",
            "command": "curl -s -X POST $SLACK_WEBHOOK -d '{\"text\":\"✅ Claude Code エージェントが PR を作成しました\"}'"
          }
        ]
      }
    ]
  }
}
```

**2. xclaude-plugin でトークンを節約しながらビルドループを回す**

```
# Claude Code への指示例（バックグラウンドで実行）
/background "xclaude-plugin の build-error-extraction ツールを使って
MyApp-iOS の Swift 6 concurrency 警告を全て修正して。
ビルドが通ったら自動でコミット・push・PR を作成して。
PR タイトルは 'fix: Swift 6 concurrency warnings' にして。"
```

エージェントは worktree 内で作業するため現在のブランチを汚染しません。修正完了の通知が Slack に届いたら GitHub で PR を確認するだけです。

**3. iOS CI での注意点**

自動 push が有効になるため、ブランチ保護ルールで **"Require pull request before merging"** を設定しておくことで、エージェントが直接 main に push するリスクを防げます。CI（GitHub Actions + fastlane）を PR トリガーに設定しておけば、draft PR の段階でビルド・テストが自動実行され、人間がレビューする時点では CI 結果も揃っている状態になります。
