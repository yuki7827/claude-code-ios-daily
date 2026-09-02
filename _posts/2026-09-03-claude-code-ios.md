---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-09-03"
date: 2026-09-03 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- **v2.1.258（2026-09-01）リリース**（[Claude Code 変更ログ](https://code.claude.com/docs/en/changelog)）— 2 件のバグ修正。① **macOS 12（Monterey）で Claude Code が起動しない問題を修正** — v2.1.255 で混入したリグレッション。Intel Mac や M1 Mac の macOS 12 環境で `claude` コマンドが即終了していたケースが解消。iOS 開発者で macOS アップグレードを保留しているチームには特に重要; ② **リモート・スケジュールセッションが再送パーミッション承認後に "user messages must have non-empty content" で失敗する問題を修正** — GitHub Actions 等の CI 環境で Claude Code をスケジュール実行しているとき、権限確認ダイアログの再送直後にセッションが空メッセージエラーで落ちていたケースが解消。

## 🛠 GitHub の動き

- [Issue #494 — set_sim_orientation: iOS Simulator の向きを Claude Code から制御するツールを追加（getsentry/XcodeBuildMCP / CLOSED 2026-09-02）](https://github.com/getsentry/XcodeBuildMCP/issues/494) — iOS シミュレーターの向き（縦向き・横向き）をエージェントから直接切り替える `set_sim_orientation` ツールが XcodeBuildMCP に追加・マージされてクローズ。これまで向きに依存する UI テスト（ランドスケープ専用レイアウトの検証など）はシミュレーター GUI を手動操作する必要があったが、Claude Code が `set_sim_orientation(orientation: "landscape")` を呼び出すだけで完結するようになる。最新版（`brew upgrade xcodebuildmcp`）への更新で利用可能。

- [Issue #492 — buildAndTest が "Planning" で永久停止する — xcodebuild stdout パイプデッドロック（getsentry/XcodeBuildMCP / OPEN）](https://github.com/getsentry/XcodeBuildMCP/issues/492) — XcodeBuildMCP がビルドプロセスを起動した後、xcodebuild の子プロセス（SWBBuildService）が stdout パイプの書き込みバッファを埋めた状態でブロックし、MCP サーバー側がバッファを読み切れずに相互デッドロックとなる問題。Xcode 26.2 以降の環境で顕在化しやすい。**暫定回避策**: `build` ツール（ビルドのみ）と `test_sim` ツール（テストのみ）を分けて呼び出すと問題を回避できる（詳細は下の今日の Tip 参照）。根本修正は調査中。

- [Issue #514 — v2.7.0 で iOS と watchOS を混合したテストプランのシミュレーターテストが失敗する（getsentry/XcodeBuildMCP / OPEN）](https://github.com/getsentry/XcodeBuildMCP/issues/514) — XcodeBuildMCP v2.7.0 以降で、iOS ターゲットと watchOS ターゲットを同一テストプランに含む場合に `test_sim` が失敗するバグ。Apple Watch 対応アプリを開発しているチームで発生の可能性がある。回避策として、iOS 用と watchOS 用のテストプランを分割して個別に実行することが推奨されている。

- [Issue #91501 — \[DOCS\] Claude Code iOS（anthropics/claude-code / OPEN 2026-09-02）](https://github.com/anthropics/claude-code/issues/91501) — iOS 開発者向けの公式ドキュメントが不足しているとして、XcodeBuildMCP 連携・CLAUDE.md テンプレート・シミュレーター操作の Tips 等をまとめた専用ドキュメントの整備を求めるドキュメント要望 Issue。コミュニティからの支持が集まっており、Anthropic 側の公式 iOS サポートドキュメントが拡充される可能性がある。

## 📝 日本語コミュニティ

- [Xcode 26.3 の新 AI 機能を実務で試して分かった 5 つのポイント（Zenn / taishikusunose）](https://zenn.dev/taishikusunose/articles/c7df06b82ce6da) — Xcode 26.3 の Claude Agent SDK ネイティブ統合を実際のプロダクション iOS プロジェクトに導入した際の気づきをまとめた記事。「Claude Code CLI とネイティブ統合の使い分け」「SwiftUI Preview をエージェントが視覚的に検証する精度」「マルチターゲットプロジェクトでのコンテキスト管理」など、実務目線での 5 つのポイントが整理されている。

- [Xcode 26.3 エージェント型コーディング入門 — Claude・Codex・MCP で開発を自動化（Qiita / kai_kou）](https://qiita.com/kai_kou/items/1b24ad62dde4c02ae4f0) — Xcode 26.3 が提供するエージェント型コーディング機能を Claude Code・OpenAI Codex の両方から利用する構成を解説した入門記事。MCP 経由で Xcode の mcpbridge に接続し、ビルド・テスト・Preview 検証・ドキュメント検索をひとつのエージェントループで完結させるセットアップ手順が詳しい。

## 🌐 海外コミュニティ / Tips

- [iOS Dev Tools: Claude XcodePreviews（iOS Dev Tools Newsletter）](https://iosdevtools.substack.com/p/ios-dev-tools-aboutkit-helpkit-claude) — iOS 開発者向けの週刊ニュースレターで「Claude XcodePreviews」が紹介。Xcode 26.3 のネイティブ統合により Claude がビルド後に SwiftUI Preview をスクリーンショットとして取得し、視覚的な不整合を自律的に修正するループが可能になったことを解説している。XcodeBuildMCP との比較（MCP 外部接続 vs IDE ネイティブ）についても言及あり。

## 💡 今日のおすすめ実践 Tip

**「XcodeBuildMCP の `buildAndTest` が "Planning" でハングするときの分割実行回避策」**

Issue #492 により、Xcode 26.2 以降の環境で XcodeBuildMCP の `buildAndTest` が "Planning" 段階から進まなくなる場合があります。原因は xcodebuild の子プロセス（SWBBuildService）が stdout パイプを詰まらせるデッドロックです。

**回避策: `build` + `test_sim` を分けて呼ぶ**

```
# ❌ 問題が起きやすいパターン（1ステップで完結させようとする）
"XcodeBuildMCP の buildAndTest で MyApp の全テストを実行してください"

# ✅ 回避策（ビルドとテストを分割して呼ぶよう Claude に指示する）
"以下の手順で進めてください:
1. まず XcodeBuildMCP の build ツールで MyApp をビルドする
2. ビルドが成功したら test_sim ツールでテストを実行する
3. それぞれの結果を確認してから次のステップに進む"
```

**CLAUDE.md に事前に記載しておく場合**

```markdown
## XcodeBuildMCP 使用上の注意
- `buildAndTest` は使用しないこと（Xcode 26.2+ 環境でパイプデッドロックが発生する）
- 必ず `build` ツールでビルドを完了させてから、`test_sim` ツールでテストを実行すること
- ビルド成功を確認してからテストに進む 2 ステップを徹底すること
```

この 2 ステップ分割により、stdout バッファの詰まりを回避しつつビルド・テストの両方を Claude Code から自動実行できます。Issue #492 の根本修正がリリースされるまでの暫定対応として有効です。
