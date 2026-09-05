---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-09-06"
date: 2026-09-06 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- **Claude Code Desktop に iOS Simulator ペインが追加（Week 30 / 2026-07-21）**（[What's New Week 30](https://code.claude.com/docs/en/whats-new/2026-w30)） — Pro・Max・Team プランで公開ベータとして提供中。Claude がアプリをビルド・起動・確認するとき、会話の隣にペインが開き、デバイス画面をライブストリームで表示する。「コンピューターユース」不要でシミュレーターを直接操作でき、画面をタップ・スワイプしてアプリを検証するループをそのまま Claude に任せられる。Xcode（iOS プラットフォームインストール済み）と Claude Desktop v1.24012.0 以上が必要。
- **Claude Security プラグインが追加（Week 30 / 2026-07-21）**（[同上](https://code.claude.com/docs/en/whats-new/2026-w30)） — Claude Code セッション内でコードベースのマルチエージェント脆弱性スキャンを実行するプラグイン。エージェントがアーキテクチャのマッピング・脅威モデリング・脆弱性探索・相互レビューを並列実行し、結果を `CLAUDE-SECURITY-<timestamp>/` ディレクトリに出力する。Swift の iOS アプリで Keychain 操作・NSURLSession 通信・UserDefaults への秘密情報保存などのセキュリティ問題を自動発見するのに活用できる。

## 🛠 GitHub の動き

- **[XcodeBuildMCP v2.7.0 リリース（2026-07-23）](https://github.com/getsentry/XcodeBuildMCP/releases)** — Xcode 27 の **Device Hub シミュレーター**に対応。UI 自動化ツールが Xcode 27 のシミュレーターでフルに動作するようになった（`ui-automation/tap`・`ui-automation/swipe` 等 59 ツール）。ビルド・テスト結果のスキーマがバージョン 3 に更新され、構造化された機械可読出力がより詳細になっている（**スキーマバリデーションを v2→v3 に更新する必要あり**）。また `xcodebuildmcp purge` コマンドや、セッションをまたいで再利用できる `extraArgs` のデフォルト設定も追加。
- **[Issue #340 — 並行セッション間の DerivedData 競合を修正（getsentry/XcodeBuildMCP / 解決済み）](https://github.com/getsentry/XcodeBuildMCP/issues/340)** — 同一プロジェクトに複数の MCP セッションや CLI 呼び出しが並行でビルドすると、共有 DerivedData ディレクトリで競合が起き、インクリメンタルビルドの破損やレースコンディションが発生していた問題。ワークスペースのルートパスをハッシュ化して DerivedData サブディレクトリを分離する方式（`~/Library/Developer/XcodeBuildMCP/DerivedData/<scheme>-<hash>/`）で解決済み。iOS 開発で複数ブランチを worktree で並列作業する際に特に恩恵が大きい。

## 📝 日本語コミュニティ

- 該当なし（2026-09-06 時点で新規の Zenn・Qiita 記事は確認できず）

## 🌐 海外コミュニティ / Tips

- [Claude Code Can Now Build and Test iOS Apps in Apple's Simulator（MacRumors / 2026-07-21）](https://www.macrumors.com/2026/07/21/claude-code-ios-simulator/) — iOS Simulator ペインのリリースを伝える MacRumors の記事。コンピューターユース不要・タップ操作を Claude が直接実行・ペインを閉じても iOS Simulator は起動したまま、といった実装の要点がまとまっている。
- [Claude Code brings live iOS app testing into its Mac app（9to5Mac / 2026-07-21）](https://9to5mac.com/2026/07/21/claude-code-brings-live-ios-app-testing-into-its-mac-app/) — 9to5Mac によるレポート。「Apple の Simulator アプリの通常ショートカットに加え、ペインから直接アプリをタップ・スワイプして操作できる」という UX のポイントと、Mac 専用機能であることが強調されている。

## 💡 今日のおすすめ実践 Tip

**「Claude Code Desktop の iOS Simulator ペインで UI 確認ループを自動化する」**

Week 30 で追加された iOS Simulator ペインを使うと、Claude がコードを修正 → ビルド → シミュレーター起動 → 画面確認 → 再修正というループを自律的に回せます。セットアップ手順と使い方をまとめます。

**前提条件**

- macOS 上の Claude Code Desktop（v1.24012.0 以上）
- Xcode（iOS プラットフォームをインストール済み）
- Pro・Max・Team プランのいずれか

**プロンプト例**

```text
> シミュレーターでアプリを起動して、オンボーディング画面のボタンが
  正しいフォントサイズ・カラーで表示されているか確認してください。
  問題があれば修正してください。
```

Claude がシミュレーターを起動すると、会話ウィンドウの横に iOS Simulator ペインが自動で開きます。

**XcodeBuildMCP との使い分け**

| シナリオ | 推奨ツール |
|--------|-----------|
| UI の見た目を Claude に視覚確認させたい | iOS Simulator ペイン（Desktop） |
| CI 環境・ヘッドレスでビルドを回したい | XcodeBuildMCP（CLI / GitHub Actions） |
| Xcode 27 Device Hub シミュレーターで動かしたい | XcodeBuildMCP v2.7.0 |

**DerivedData の競合を避けるための補足**

XcodeBuildMCP v2.7.0 以降は `derivedDataPath` を明示していなければ自動でワークスペースごとに DerivedData を分離します（Issue #340 の修正）。worktree を使って複数ブランチを並行作業している場合も安心して使えます。
