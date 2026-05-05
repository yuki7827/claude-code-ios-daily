---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-05-06"
date: 2026-05-06 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Claude Code v2.1.128 Changelog (May 4)](https://code.claude.com/docs/en/changelog) — `/mcp` コマンドが接続済みサーバーのツール数を表示し 0 ツールのサーバーにフラグを立てるよう改善。再接続時の全ツール名フラッドも解消され、サーバープレフィックスで集約表示に。iOS 向け MCP サーバーの接続状況が一目で把握できる。
- [Claude Code v2.1.126 — claude project purge (May 1)](https://code.claude.com/docs/en/changelog) — `claude project purge [path]` が追加。プロジェクトの Claude Code 状態（キャッシュ・会話履歴）を丸ごと削除でき、Xcode プロジェクトの状態がおかしくなったときのリセット手段として活用できる。

## 🛠 GitHub の動き

- [Xcode 26.3 + Claude Agent: Model Swapping, MCP, Skills, and Adaptive Configuration](https://itnext.io/xcode-26-3-claude-agent-model-swapping-mcp-skills-and-adaptive-configuration-546c27e18e66) — fatbobman による実践ガイド。Xcode 内蔵の `claude` バイナリを最新版に差し替えて Opus 4.6 を使う手順、MCP 設定ファイルの場所（`~/Library/Developer/Xcode/CodingAssistant/ClaudeAgentConfig/.claude`）、スキル配置先（同 `skills/`）を詳解。
- [Xcode 26.3: Use AI Agents from Cursor, Claude Code & Beyond](https://dev.to/arshtechpro/xcode-263-use-ai-agents-from-cursor-claude-code-beyond-4dmi) — DEV Community の解説記事。Cursor・Claude Code・Codex など複数エージェントを Xcode 26.3 の MCP 経由で使い分ける設定手順を整理。グローバル MCP 設定が Xcode Agent に引き継がれない点と回避策を解説。

## 📝 日本語コミュニティ

- [コードを1行も書かずに25,000行のiOSアプリを作った話 ― Claude Codeで完全Vibe Coding](https://qiita.com/john-rocky/items/b5a24858e797c954b16f) — 11 日間・136 コミットで SwiftUI + SwiftData + FoundationModels + AVFoundation の iOS アプリ「Kaputalk」を完成させた実録。リリース 7 日で 500 ダウンロードを達成。Vibe Coding はゼロ知識向けではなく、設計・品質管理に注力できるプログラマー向け手法であるという指摘が刺さる。
- [iOSアプリ開発未経験者がClaudeCodeを使って1週間でリリースしたお話](https://qiita.com/chatrate/items/bd2703d68a2bb8fffe08) — Swift 未経験者が Claude Code だけで 1 週間で App Store にリリースした体験記。CocoaPods・証明書まわりなど非エンジニアがつまずきやすいポイントの対処法を記録。
- [Claude Code スキルが膨れ続けた 15 日間 — 3 回の棚卸しで学んだこと](https://qiita.com/shimo4228/items/081b2f5efbc4b6cbbbd1) — 15 日でスキルが 16→48 件に増殖した経験と整理法。`~/.claude/skills/learned/` に自動蓄積される「学習済みスキル」が複数 iOS プロジェクト分混在する問題と、プロジェクト別に整理するコツをまとめた実践レポート。

## 🌐 海外コミュニティ / Tips

- [Xcode 26.3 + Claude Agent: Adaptive Configuration (fatbobman, English)](https://fatbobman.com/en/posts/xcode-263-claude/) — 環境変数 `CLAUDE_CONFIG_DIR` を使い、ターミナル版 Claude Code と Xcode 内蔵 Claude Agent で異なる設定を自動切り替えする "Adaptive Configuration" パターンを紹介。ひとつの CLAUDE.md を両環境で使い回せるのが利点。
- [MCP Servers Cost 67,000 Tokens Before You Type a Word (r/ClaudeCode)](https://www.morphllm.com/claude-code-reddit) — Reddit コミュニティの報告。MCP サーバー 4 本を接続するだけでシステムプロンプト・ツール定義・JSON スキーマが 3〜4 万トークンを消費することが判明。iOS 開発では XcodeBuildMCP と Xcode 純正 MCP のどちらかに絞るトレードオフを意識する価値がある。

## 💡 今日のおすすめ実践 Tip

**Xcode 内蔵 Claude を最新バイナリに差し替えて Opus 4.6 を使う**

Xcode 26.3 に同梱されている `claude` バイナリは古いバージョン（Opus 4.5 相当）のままの場合があります。fatbobman の記事によると、`~/Library/Developer/Xcode/CodingAssistant/Agents/Versions/26.3/` 内の `claude` バイナリを Anthropic が配布する最新 CLI に差し替えると、Xcode の Model ピッカーで Opus 4.6 が選択可能になります。あわせて、グローバルに設定した MCP サーバーは Xcode Agent に引き継がれないため、`~/Library/Developer/Xcode/CodingAssistant/ClaudeAgentConfig/.claude` に別途 `config.toml` を配置する必要があります。ターミナル版と Xcode 版の設定を `CLAUDE_CONFIG_DIR` で自動切り替えする Adaptive Configuration と組み合わせると、IDE 内でも最新モデルと MCP ツールをフル活用できます。
