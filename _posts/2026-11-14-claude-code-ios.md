---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-11-14"
date: 2026-11-14 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Apple の公式 Xcode プラグインが Claude Code 公式マーケットプレイスに追加（Anthropic）](https://www.anthropic.com/news/apple-xcode-claude-agent-sdk) — 2026-11-06、Anthropic の公式プラグインリポジトリ `claude-plugins-official` に Apple 製の Xcode プラグインが PR #6338 でマージ。`/plugin install xcode@claude-plugins-official` 1 コマンドでインストールでき、ビルド・テスト・SwiftUI プレビュー・シミュレーター / 実機操作・ビルドログ読み取り・Apple Developer ドキュメント検索が利用可能になる。最新 Xcode（Xcode 26 以降）が必須で、プラグインの実体は Xcode 付属の `xcrun agent plugin path` によるローカルバイナリのリンクのため、Xcode を更新すれば自動的にプラグインも最新版に追従する。

## 🛠 GitHub の動き

- [I vibe-coded an iOS debug tool that lets Claude Code drive my app（dev.to / foxgem）](https://dev.to/foxgem/i-vibe-coded-an-ios-debug-tool-that-lets-claude-code-drive-my-app-2295) — Claude Code → MCP サーバー → iproxy（USB）→ アプリ内の軽量 HTTP サーバーというチェーンで、実機 iOS アプリを Claude Code がリモート操作できるデバッグツールを構築した実践例。スクリーンショット取得・画面上のアクション一覧取得・アクションのトリガーなど 6 つの MCP ツールを公開し、コードの大半は Claude が生成。すべて `#if DEBUG` ブロックに閉じているためリリースビルドへの混入を防いでいる。

## 📝 日本語コミュニティ

- [Appleが競合のClaude Code向けに公式Xcodeプラグインを公開、シミュレーター操作もAI任せに（hashout.jp）](https://hashout.jp/?p=14062) — Apple が Anthropic 製 Claude Code 向けに公式 Xcode プラグインを公開したことを日本語で速報。PR #6338 のマージ内容と、ビルド・テスト・SwiftUI プレビュー・シミュレーター操作まで Claude Code が担えるようになった点を解説。Apple 自身が競合他社製ツールを公式サポートした異例の動きとして注目を集めている。

## 🌐 海外コミュニティ / Tips

- [Apple releases official Xcode plugin for Claude Code（iphonewired.com）](https://iphonewired.com/news/1200319/) — Apple の Xcode プラグインが Claude Code 公式マーケットプレイスに登場した経緯と技術的背景を詳述。プラグインのソースがリポジトリではなくローカルの `xcrun` コマンドへのリンクであること、Xcode 26.3 で既に実現していた Claude Agent SDK 連携との関係なども整理されている。

- [I gave Claude Code a simulator and it started tapping and taking screenshots on its own（dev.to / shimo4228）](https://dev.to/shimo4228/i-gave-claude-code-a-simulator-and-it-started-tapping-and-taking-screenshots-on-its-own-57bj) — iOS シミュレーターへのアクセスを Claude Code に与えたところ、指示なしで自律的にタップ・スクリーンショット撮影・ログ確認まで行い始めた体験レポート。エージェントが UI を探索しながらバグを特定していく過程が詳述されており、MCP + シミュレーター連携の実用イメージをつかむのに最適。

## 💡 今日のおすすめ実践 Tip

**Apple 公式 Xcode プラグインの即セットアップと活用手順**

```bash
# 1. Claude Code で公式プラグインをインストール
/plugin install xcode@claude-plugins-official

# 2. インストール済みプラグインを確認
/plugins

# 3. プロジェクトの CLAUDE.md にビルド・テスト用スキームを明示（例）
# ## Build & Test
# - build: xcodebuild -scheme MyApp -destination 'platform=iOS Simulator,name=iPhone 16'
# - test:  xcodebuild test -scheme MyAppTests -destination 'platform=iOS Simulator,name=iPhone 16'
```

プラグイン導入後は「`xcodebuild` でビルドして失敗箇所を修正して」と Claude Code に指示するだけで、ビルドログ読み取り → 該当コード修正 → 再ビルドのサイクルを自律的に回してくれる。SwiftUI プレビューの確認や Apple Developer ドキュメントの参照も Claude から直接指示できるため、エディタと Xcode を往復するコストを大幅に削減できる。なお `xcrun agent plugin path` の実態は Xcode 付属のローカルバイナリへのリンクなので、Xcode を更新するだけでプラグインも自動的に最新版へ追従する点は押さえておきたい。
