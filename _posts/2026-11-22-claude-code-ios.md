---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-11-22"
date: 2026-11-22 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [v2.1.329](https://www.havoptic.com/r/claude-code-2.1.329) — `/tasks` コマンドがスケジュールタスクとフォークも表示するように拡張。`/status` がマーケットプレイスの自動更新が無効なチャンネルを警告表示するように変更。サインアウト処理・`/teleport` コマンド・ファイル処理まわりの修正が多数含まれる。iOS CI でスケジュール起動を使っているチームは `/tasks` で稼働中の自動タスク一覧を確認しやすくなった。

## 🛠 GitHub の動き

- [ApptitudeLabs/claude-code-ios-dev-setup（GitHub）](https://github.com/ApptitudeLabs/claude-code-ios-dev-setup) — PRD 駆動ワークフロー・ultrathink（`/think` 拡張推論）・Swift/SwiftUI iOS 開発を組み合わせた Claude Code セットアップガイド。サンプル `CLAUDE.md`（Swift 6 / iOS 18 以上 / SwiftUI + SwiftData 前提）が付属しており、ゼロから iOS プロジェクトを始める際のテンプレートとして使いやすい。同趣旨のガイドとして [keskinonur/claude-code-ios-dev-guide](https://github.com/keskinonur/claude-code-ios-dev-guide) も同時期に注目を集めており、こちらは PRD → Plan → Implement の 3 ステップサイクルと計画フェーズの分離を重視した構成になっている。

## 📝 日本語コミュニティ

- [Xcode 27 の公式スキルを Claude Code から使ってみた（Classmethod）](https://dev.classmethod.jp/articles/xcode-agent-skills-claude-code/) — Apple が Xcode 27 に同梱する公式 Agent Skills（`audit-xcode-security-settings`・`app-intents-whats-new-27` ほか計 7 本）を Claude Code から有効にした際の実測レポート。スワイプ削除リストの実装を「スキルあり」vs「スキルなし」で比較したところ、スキルあり版は `swipeActions` を使った 31 行のコードが生成されたのに対し、スキルなし版は 160 行のカスタム実装になった。読み込みは `CLAUDE_CODE_PLUGIN_DIRS` に `xcrun agent plugin path` の出力先を追加するだけで完了し、スキルには `xcode-integration:` プレフィックスが付与される。

## 🌐 海外コミュニティ / Tips

- [Give your simulator superpowers（avanderlee.com / Antoine van der Lee）](https://www.avanderlee.com/ai-development/using-xcode-27s-agent-skills-in-claude-codex-and-cursor/) — Antoine van der Lee による Xcode 27 の Agent Skills を Claude Code / Codex / Cursor で活用するガイド。`xcrun agent skills export` コマンド 1 本で Xcode 27 に内蔵された SwiftUI 特化スキルをローカルに書き出し、各エージェントツールに読み込む手順を詳述。エージェントがシミュレーターを「見ながら」UIバグを修正するユースケースを動画付きで紹介しており、MobileBuildMCP との組み合わせも言及されている。

- [Xcode 27 Agent Skills Export: One-Command Workflow（blakecrosley.com）](https://blakecrosley.com/blog/xcode-27-agent-skills-export) — `xcrun agent skills export --output-dir .claude/skills/` で Xcode 27 のスキルを Claude Code のスキルディレクトリに直接書き出す手順を解説。エクスポート後に `/skills` コマンドで確認すると `xcode27/*` スキルが一覧表示されることを確認しており、プロジェクト `CLAUDE.md` から `@skill xcode27/swiftui-best-practices` のように参照できる点も紹介。Xcode バージョンが変わるたびに再エクスポートが必要な点に注意。

## 💡 今日のおすすめ実践 Tip

**`xcrun agent skills export` で Xcode 27 純正スキルを Claude Code に読み込む**

Apple が Xcode 27 に同梱した公式 Agent Skills は、`xcrun agent plugin path` を `CLAUDE_CODE_PLUGIN_DIRS` に設定する方法と、`xcrun agent skills export` でファイルとして書き出す 2 つの方法で Claude Code に読み込める。後者はプロジェクトリポジトリにコミットできるため、チーム全員が同じスキルセットを使えるメリットがある。

```bash
# 1. プロジェクト内のスキルディレクトリに書き出す
xcrun agent skills export --output-dir .claude/skills/

# 2. 読み込まれているか確認
# Claude Code 内で
/skills
# → xcode27/swiftui-best-practices, xcode27/app-intents-whats-new-27 などが表示される

# 3. CLAUDE.md から参照する例
# ## Rules
# - コードレビュー時は @skill xcode27/swiftui-best-practices を参照すること
```

Xcode を更新した際はスキルも再エクスポートする（Xcode のビルドごとにパスが変わるため `plugin path` 方式は CI 環境で不安定になりやすく、`export` 方式の方が安定する）。
