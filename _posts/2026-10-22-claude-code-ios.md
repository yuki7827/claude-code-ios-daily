---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-10-22"
date: 2026-10-22 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [v2.1.305（2026-10-20）](https://code.claude.com/docs/en/changelog) — Linux namespace サンドボックス対応を追加。ワークツリー・MCP・クラウドセッションにわたる複数の安定性修正も含む。`git worktree` でブランチごとに Xcode プロジェクトを分離している iOS 開発者にも恩恵があるとみられる。
- [v2.1.304](https://code.claude.com/docs/en/changelog) — ガイド付き auto-mode セットアップ UI と新しいコードレビューコントロールを追加。

## 🛠 GitHub の動き

- [#103991 iOS Simulator パネルがホームボタン端末をノッチ付きフレームに横方向に引き伸ばして表示（2026-10-21 open）](https://github.com/anthropics/claude-code/issues/103991) — iPhone SE 等の旧型（ホームボタン付き）端末のシミュレーターを Claude Code Desktop パネルで表示すると、誤ってノッチ付きフォルムに強制リサイズされる問題。UI 確認ワークフローで実機イメージと乖離が生じる。

## 📝 日本語コミュニティ

- [Xcode 27 の公式スキルを Claude Code から使ってみた（dev.classmethod.jp）](https://dev.classmethod.jp/articles/xcode-agent-skills-claude-code/) — `xcrun agent plugin path --plugin-format claude` で Claude Code 用プラグインを出力し `CLAUDE_CODE_PLUGIN_DIRS` に追加するだけで、Xcode 27 付属の公式スキル（SwiftUI・Security・App Intents 等）が `xcode-integration:` プレフィックス付きで Claude Code から利用可能になる手順を実機検証した記事。
- [Swift 未経験でも Claude Code で iPhone アプリを審査提出できた（Qiita / masa1124）](https://qiita.com/masa1124/items/6b66b5f27158e7067ff9) — 競合リサーチ → CLAUDE.md に仕様を記述 → 実装の流れで、Swift 未経験の著者が Claude Code を活用して App Store 審査まで到達した事例。ビルドとテストは Xcode で著者が担当、コード生成は Claude Code に委ねるという役割分担が実践的。

## 🌐 海外コミュニティ / Tips

- [Give your simulator superpowers: Using Xcode 27's Agent Skills in Claude Code（avanderlee.com）](https://www.avanderlee.com/ai-development/using-xcode-27s-agent-skills-in-claude-codex-and-cursor/) — Xcode 27 の agent skills を Claude Code・Codex・Cursor に読み込む手順と、各スキルがシミュレーター操作・SwiftUI プレビュー・ドキュメント検索でどう機能するかを解説。`xcrun agent skills export ~/.agents/skills` で多くのエージェントが自動検出する場所に展開できる方法も紹介。
- [Xcode 27 Agent Skills Export: Apple AI Guidance（blakecrosley.com）](https://blakecrosley.com/blog/xcode-27-agent-skills-export) — `xcrun agent skills export` コマンドで Xcode 27 付属スキルをエクスポートし Claude Code で使う手順を詳説。スキルフォルダーの構造と各スキルのカバー範囲（SwiftUI・Security チェック・App Intents など）の一覧が参考になる。

## 💡 今日のおすすめ実践 Tip

**`xcrun agent plugin path` で Xcode 27 公式スキルを 1 コマンド導入**

Xcode 27 GA（9/14 リリース、要 macOS 26 以降・Apple Silicon）を使っていれば下記で即セットアップできる。

```bash
# プラグインパスを確認
xcrun agent plugin path --plugin-format claude

# ~/.claude/settings.json に追加（または環境変数で設定）
# export CLAUDE_CODE_PLUGIN_DIRS="<上記のパス>"
```

プロジェクトの `.claude/settings.json` に `pluginDirectories` として書けばチームで共有できる。読み込まれたスキルは `/skills` で `xcode-integration:` プレフィックス付きで確認できる。SwiftUI ベストプラクティス・Security ビルド設定チェック・App Intents API ガイドなど Apple 公式コンテンツがそのまま Claude Code のスキルとして使える。
