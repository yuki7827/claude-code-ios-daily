---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-06-23"
date: 2026-06-23 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- 本日時点の最新版は v2.1.185（2026-06-20）— 新リリースなし。なお [anthropics/claude-code #70108](https://github.com/anthropics/claude-code/issues/70108) にて、Claude iOS アプリ（App Store 最新版）が Claude Code セッションへの接続時にクラッシュする不具合が 2026-06-22 に報告済み。iOS アップデート後から発生しているとの声が複数あり、公式の修正対応は未確認。iOS アプリから Claude Code セッションへ接続している方は注意が必要。

## 🛠 GitHub の動き

- [getsentry/XcodeBuildMCP — 2026-06 新機能](https://github.com/getsentry/XcodeBuildMCP/releases) — 直近リリース群で 3 つの機能を追加。**`xcodebuildmcp upgrade`** コマンド（`--check` で確認のみ・`--yes` でノンインタラクティブアップグレード）、**platform-aware セットアップウィザード**（iOS / macOS / tvOS / watchOS / visionOS を選択してオンボーディング。macOS 専用プロジェクトではシミュレータ質問をスキップ）、**per-test timing**（テストごとの実行時間を出力し遅いテストをエージェントが特定しやすくなる）。UI 自動化のタップガイドも label/ID 優先に更新され、エージェントエラーが減少。

- [jamesrochabrun/AgentHub](https://github.com/jamesrochabrun/AgentHub) — Claude Code と Codex の全セッションを一元管理するネイティブ macOS アプリ。worktree 作成・複数ターミナルの並列実行・変更のプレビュー確認・diff からのインライン修正を GUI で操作可能。Apple 署名済み・Sparkle 自動更新対応。iOS プロジェクトで「ビルドエージェント」と「テストエージェント」を並列実行する際のセッション管理に有用。外部へのデータ送信なし（ローカル完結）。

- [siteboon/claudecodeui](https://github.com/siteboon/claudecodeui) — Claude Code・OpenCode・Cursor CLI・Codex をモバイル・ウェブブラウザから操作できるオープンソース Web UI（CloudCLI）。iPhone/iPad のブラウザから Mac 上の Claude Code セッションをリモート操作でき、SSH 不要で外出先から iOS ビルドの状況確認や指示出しができる。

## 📝 日本語コミュニティ

- [XcodeBuildMCP×Claude Codeスキルシステムで、iOSビルドを自動化する（Zenn / kyoichi）](https://zenn.dev/kyoichi/articles/claude-code-xcodebuildmcp-ios-build) — XcodeBuildMCP をサブエージェントに委譲することでメインエージェントのコンテキスト消費を抑える「スキルシステム活用」の手順を解説。06-21 に紹介したプラグイン版の発展形で、79+ ツールをワークフロー単位でサブエージェントへ委譲する設計が核心。

- [Xcode 26.3 で iOS 開発の AI 活用はどう変わるのか ── タップルでの導入方針とあわせて（CyberAgent Developers Blog）](https://developers.cyberagent.co.jp/blog/archives/62551/) — マッチングアプリ「タップル」の iOS チームが Xcode 26.3 の MCP 統合を受けた AI 活用方針を公開した企業ブログ。BuildProject・RenderPreview・DocumentationSearch の使い分けポリシー、pbxproj などの変更禁止ファイルリスト設定方針を実例とともに解説。大規模チームでの Claude Code 活用ルール整備の参考になる。

- [Xcode 26.3のMCPとは何か — なぜ必要で、どう使うのか。7つのSkillsで実践する（Qiita / dsgarage）](https://qiita.com/dsgarage/items/7c4b13de4dd4b2ec2370) — MIT ライセンスの `dsgarage/XcodeMCP-Skills` を使った Xcode 26.3 MCP × スキルの実践ガイド。`/ios-build-test --save-log` コマンドで目標入力→コード生成→ビルド→エラー確認→修正→SwiftUI プレビュー→テストの自律ループを回す手順と、pbxproj 変更禁止の安全設定を収録。Zenn 版（https://zenn.dev/dsgarage/articles/xcode-263-mcp-skills）も同内容で公開。

## 🌐 海外コミュニティ / Tips

- [XcodeBuildMCP: Build & Test iOS Apps from Claude — Complete Guide 2026（mcp.directory）](https://mcp.directory/blog/xcodebuildmcp-complete-guide-2026) — Sentry 管理下の XcodeBuildMCP（82 ツール・16 ワークフローグループ）の現状と Claude Code への組み込み方法をまとめた英語ガイド。編集→ビルド→検証の自律ループを回すセットアップ手順と、各ツールグループの使い分けを網羅。初めて XcodeBuildMCP を導入する際のリファレンスとして有用。

- [AgentHub README（jamesrochabrun/AgentHub）](https://github.com/jamesrochabrun/AgentHub/blob/main/README.md) — ClaudeCodeSDK をベースに Process で Claude Code CLI を呼び出す設計の詳細と、worktree を使ったマルチエージェント並列実行のユースケースを解説。iOS プロジェクトでエージェントを並列実行する際の参考設計として活用できる。

## 💡 今日のおすすめ実践 Tip

**「XcodeBuildMCP をサブエージェントに委譲してメインコンテキストを守る」**

XcodeBuildMCP の 79+ ツールをフル活用するとビルドログがメインエージェントのコンテキストを圧迫しやすい。kyoichi さん考案のスキルシステム構成では、XcodeBuildMCP 操作をサブエージェントへ委譲することでこの問題を回避できます。

Claude Code への指示例:

```
あなたはオーケストレーターです。以下の作業をサブエージェントに委譲してください。

【サブエージェントへの指示】
XcodeBuildMCP を使って次のステップを順に実行してください:
1. BuildProject で MyApp-iOS スキームをビルド
2. エラーが出たら GetBuildLog で詳細を確認し、XcodeUpdate で修正
3. エラーが解消したら RunSomeTests でユニットテストを実行
4. per-test timing を出力し、5 秒超のテストを一覧化
5. RenderPreview で変更した SwiftUI ビューのプレビューを確認

作業完了後、結果のサマリー（ビルド結果・失敗テスト数・遅いテスト一覧）だけを
私（オーケストレーター）に返してください。詳細ログは不要です。
```

サブエージェントがビルドログを全量受け取っても、メインエージェントへは「サマリーのみ」が戻るため、コンテキスト枯渇を防ぎつつ XcodeBuildMCP の全機能を活用できます。`xcodebuildmcp upgrade` で常に最新版を維持しておくと、per-test timing など 2026-06 追加の新機能がすぐ使えるようになります。
