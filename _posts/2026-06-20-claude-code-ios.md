---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-06-20"
date: 2026-06-20 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Claude Code v2.1.183（2026-06-19）](https://code.claude.com/docs/en/changelog) — auto モードの破壊的 git コマンドに安全ガードが追加。`git reset --hard`・`git checkout -- .`・`git clean -fd`・`git stash drop` は「ローカル作業を捨てる」と明示的に指示していない場面でブロックされるようになり、`git commit --amend` も当該セッションでエージェントが作ったコミット以外への適用が禁止された。また `attribution.sessionUrl` 設定を `false` にすると、Web/Remote Control セッションが自動生成するコミット・PR 本文から claude.ai セッションリンクを省略できる。iOS CI 環境でエージェントに xcodebuild や fastlane を自律実行させる際に、誤った `--amend` や `clean -fd` でワーキングツリーを吹き飛ばされるリスクが低減される。

## 🛠 GitHub の動き

- [Wooder/xcagents（2026-06-17 公開）](https://github.com/Wooder/xcagents) — App Store 審査前チェックと Swift コードレビューを自動化する Claude Code エージェント集。**Riley Guardian**（`/appstore-reviewer`）は ATT 同意フロー・プライバシーマニフェスト・プッシュ通知文言・アカウント削除要件など 10 分野を App Store ガイドラインの条文番号付きで審査し、最終判定を "READY TO SUBMIT" / "HOLD" で返す。**Cody Codecraft**（`/ios-code-reviewer`）は機能性・アーキテクチャ・セキュリティ・パフォーマンス・Swift 並行処理など 20 カテゴリを網羅し、blocking/warning の重要度付きフィードバックを出力する。シェルスクリプト一発でどの Xcode プロジェクトにも drop-in 導入できる MIT ライセンス。

- [conorluddy/xc-mcp（★90）](https://github.com/conorluddy/xc-mcp) — Xcode CLI ツール群・iOS シミュレータを Claude Code から扱う MCP サーバー。最大の特徴は**progressive disclosure**（段階的開示）による大幅なトークン削減で、`simctl list` の生出力 57,000 トークンを 2,000 トークンのサマリーに圧縮（96% 削減）するほか、`--mini` パラメータでビルドのみ実行しつつコンテキストフットプリントを最小化できる。29 ツールを搭載し、ビルドログの巨大出力によってコンテキストが枯渇するのを防ぐ設計は、長時間の Claude Code セッションで Xcode を連続操作する場合に効果的。

- [bitomule/mav](https://github.com/bitomule/mav) — AI コーディングエージェント向けの iOS アプリ確定的検証（deterministic validation）CLI。AXe・idb・Baguette・simctl を統一 API として抽象化し、エージェントが「UI ツリーを取得する」「ボタンをタップする」といった指示を出すだけで適切なバックエンドへルーティングされる。YAML 形式のfeature validation flow（セットアップ → UI 操作 → アサーション → スクリーンショット/ビデオ/ネットワークログの証拠生成）を記述でき、AI が推量した座標に依存しない構造的なアクセシビリティツリー操作で再現性を確保。Undolly・Boxy 等の実アプリ開発で利用中。

## 📝 日本語コミュニティ

- [Copilot VisionとClaude CodeでiOSアプリを新規作成して比較（Zenn / yamazaking）](https://zenn.dev/yamazaking/articles/copilot-vision-claude-code-ios) — GitHub Copilot Vision と Claude Code を使って同じ iOS アプリを新規作成し、コード品質・操作感・失敗ケースを実際に比較した記事。どちらが iOS 開発に向いているかを実践ベースで評価している。

- [週末アプリ開発者のための、Claude Code × iOSアプリ開発 一気通貫ガイド（Qiita / tobaru-hideyasu）](https://qiita.com/tobaru-hideyasu/items/f9ed60b136d95960b1d0) — 週末・個人開発者を想定した Claude Code × iOS アプリ開発の入門から App Store 提出までを一気通貫で解説する Qiita 記事。プロジェクト構成・CLAUDE.md 設計・XcodeBuildMCP 連携・TestFlight 配布までをステップ形式でまとめている。

## 🌐 海外コミュニティ / Tips

- [iOS Project Claude Code Setup Prompt（GitHub Gist / joelklabo）](https://gist.github.com/joelklabo/6df9fa603bec3478dec7efc17ea44596) — XcodeBuildMCP × Claude Code の完全セットアップを自動化するプロンプト集。実行すると `.claude/` ディレクトリ構造（commands・hooks・scripts・ドキュメント）を自動構築し、プロジェクト固有のスキーム・シミュレータ・依存関係を検出して CLAUDE.md を生成、`/build`・`/test`・`/run`・`/device-build` の各スラッシュコマンドをプロジェクトに合わせて作成する。ゼロから Claude Code × Xcode 環境を整える際のスターターとして使いやすい。

- [markdavidgan/apple-dev-skills](https://github.com/markdavidgan/apple-dev-skills) — Claude Code・Cursor・Kimi Code・Codex CLI など 6 エージェント対応の Apple プラットフォーム開発スキルセット。Design（Liquid Glass・アクセシビリティ）・Engineering（Swift 6・SwiftUI・SwiftData）・Product（ASO・分析・PRD）・ASC（App Store Connect 自動化）・Quality（テスト・プライバシー・セキュリティ）・Workflow（コードレビュー・マージチェック）の 6 カテゴリに 44 スキル、7 エージェント、19 コマンド、App Store Connect と Apple ドキュメント検索の 2 MCP サーバーを収録。

## 💡 今日のおすすめ実践 Tip

**「v2.1.183 の auto モード安全ガードを前提に、xcagents の App Store レビュアーを CI パイプラインに組み込む」**

今日紹介した v2.1.183 の安全ガードは、`/appstore-reviewer` のような自律エージェントを安心して動かせる土台になります。

```bash
# 1. xcagents を Xcode プロジェクトに導入
cd /path/to/YourApp
curl -sSL https://raw.githubusercontent.com/Wooder/xcagents/main/install.sh | bash
```

Claude Code への指示例:

```
1. /appstore-reviewer を実行して、現在のアプリが App Store の
   プライバシーマニフェスト・ATT 同意フロー・アカウント削除要件を
   満たしているか確認して。

2. HOLD 判定が出た項目を修正して。
   修正後は git add → git commit してよいが、
   git reset --hard や git commit --amend は使わないこと。

3. 修正が完了したら /ios-code-reviewer でコードレビューを通して、
   blocking 判定がすべて解消されたことを確認して。
```

v2.1.183 では「明示的に指示していない `git reset --hard`」がブロックされるため、エージェントが審査修正の途中でワーキングツリーをリセットして変更を失う事故が起きにくくなっています。App Store 提出直前の最終チェックをエージェントに任せる際は、このガードを意識した指示文を書いておくと安全です。
