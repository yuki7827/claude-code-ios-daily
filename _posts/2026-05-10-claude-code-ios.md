---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-05-10"
date: 2026-05-10 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Claude Code v2.1.137 (May 9)](https://github.com/anthropics/claude-code/releases) — VS Code 拡張が Windows で起動しないバグを修正。iOS 開発者の多くは Mac 利用のため直接影響は薄いが、Windows 機でコードレビューを行うチームメンバーがいる場合は更新を。
- [Claude Code v2.1.138 (May 9)](https://github.com/anthropics/claude-code/releases) — 内部修正のみ。ユーザー向けの変更点なし。

## 🛠 GitHub の動き

- [conorluddy/ios-simulator-skill](https://github.com/conorluddy/ios-simulator-skill) (⭐967、v1.4.0) — 22 本のスクリプトで iOS シミュレーターを自動化するスキル。最大の特徴は**トークン最適化**で、通常 200 行超になる xcodebuild 出力を 5 行のサマリーに圧縮して返す。アクセシビリティ属性でUI要素を意味的に検出するため、ピクセル座標依存のフラジャイルな操作を排除。`/plugin marketplace add conorluddy/ios-simulator-skill` で追加（同作者の xclaude-plugin とは別リポ）。
- [rshankras/claude-code-apple-skills](https://github.com/rshankras/claude-code-apple-skills) (⭐331) — iOS・macOS・iPadOS を横断する 149 スキル・23 カテゴリの大型コレクション。iOS 専用スキルは 7 本（コードレビュー・UI レビュー・ナビゲーション・iPad・マイグレーション・アクセシビリティ等）を含む。Liquid Glass 対応・HIG 準拠チェック・App Store 最適化スキルも内包。LobeHub と Cult of Claude のマーケットプレイスから検索・インストール可能。
- [JasperSui/claude-code-iterm2-tab-status](https://github.com/JasperSui/claude-code-iterm2-tab-status) — Claude Code のセッション状態を iTerm2 のタブタイトルにリアルタイム表示するプラグイン。⚡ 処理中 / 💤 待機中 / 🔴 要確認（オレンジフラッシュ）の 3 状態で管理。Hooks API でセッションイベントを監視するため画面スクレイピング不要。複数 Xcode プロジェクトで並行 Claude セッションを走らせる iOS 開発者に特に有用。

## 📝 日本語コミュニティ

- [Xcode 26.3エージェント型コーディング入門 — Claude・Codex・MCPで開発を自動化](https://zenn.dev/kai_kou/articles/021-xcode-26-3-agentic-coding-guide) (kai_kou, Zenn) — Claude Code・Codex・MCP を組み合わせた Xcode 26.3 エージェント型コーディングの入門ガイド。XcodeBuildMCP 設定から Claude Agent との連携まで、Xcode 内蔵 AI コーディングを始めるための手順を整理。
- [Xcode 26.3 で ANTHROPIC_BASE_URL をカスタム設定して Claude Agent を使う](https://kusumoto.app/blogs/xcode-claude-custom-endpoint) (kusumoto.app) — 環境変数 `ANTHROPIC_BASE_URL` を Xcode 26.3 の Claude Agent 設定に仕込むことで、AWS Bedrock・社内 API プロキシ・Claude Console の API キーなど、標準の claude.ai 認証以外のエンドポイントに向け直す方法を解説。Enterprise 環境でゼロトラストの API ゲートウェイを経由させたい iOS チームに実用的。

## 🌐 海外コミュニティ / Tips

- [keskinonur/claude-code-ios-dev-guide](https://github.com/keskinonur/claude-code-ios-dev-guide) — PRD 駆動ワークフロー・Ultrathink（最大 32K トークン思考）・XcodeBuildMCP 連携・Plan モードをセットにした Swift/SwiftUI iOS 開発特化の Claude Code 設定ガイド。カスタムスラッシュコマンド・エージェントスキル・Hooks のテンプレートも同梱。チームの CLAUDE.md 設計の参考として活用できる。
- [Claude Code Parallel Instances Workflow for iOS](https://www.aiqnahub.com/claude-code-parallel-instances-workflow/) — 複数 Claude セッションを git worktree + tmux で並行実行するパターンの解説。認証フロー・UI コンポーネント・テストなど依存のない iOS フィーチャーを別ワーキングツリーで同時に走らせ、翌朝にはマージ可能な独立 PR が複数出来上がっている状態を実現。`worktree.baseRef: "head"` 設定（v2.1.133 で追加）と組み合わせると WIP コミットも引き継がれる。

## 💡 今日のおすすめ実践 Tip

**ios-simulator-skill でシミュレーター操作のトークン消費を 97% 削減する**

`conorluddy/ios-simulator-skill` の最大の価値はトークン節約にあります。素の xcodebuild 出力は警告・ビルドフラグ・タイムスタンプなどで 200 行超になりがちですが、このスキルは Claude に渡す前に 5 行のサマリーに圧縮します。

インストール後は以下のように使えます。

```
/plugin marketplace add conorluddy/ios-simulator-skill

# 使用例（Claude への指示）
「アプリをシミュレーターで起動して、ホーム画面に「タスク追加」ボタンが表示されているか確認して」
```

スキルが内部的に `xcodebuild build` → `xcrun simctl launch` → アクセシビリティ API でボタン存在確認 → 結果を 1 行で返答、という流れを自動実行します。XcodeBuildMCP の 82 ツールをフル展開する構成と比較すると、シンプルな「ビルド→起動→確認」ループではこちらの方がコンテキストを食いません。複数フィーチャーを並行開発する worktree 構成と組み合わせると、各エージェントのトークン消費を最小化しながら UI 検証を回し続けられます。
