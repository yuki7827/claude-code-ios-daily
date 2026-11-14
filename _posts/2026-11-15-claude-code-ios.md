---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-11-15"
date: 2026-11-15 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [v2.1.323](https://code.claude.com/docs/en/changelog) — 実験的な **Code Mode** を追加（デフォルトオフ）。有効にすると Claude がツール呼び出しを JavaScript コードとして実行する。また `Bash` 許可ルールに `*` を含む変数（例: `PORT=* npm run dev`）が使われていると警告を出すように変更。iOS CI の `xcodebuild` ラッパースクリプト等でワイルドカードを使っている場合は設定を見直す契機になる。
- [v2.1.325](https://code.claude.com/docs/en/changelog) — zstd レスポンスデコードのセキュリティ修正と、長期セッションがコンテキスト長エラーで落ちる問題の対策を含む。大規模 iOS プロジェクトでビルドログが長大になるセッションでの安定性向上が期待できる。

## 🛠 GitHub の動き

- 該当なし（2026-11-15 時点で iOS / Swift / Xcode 関連の新規マージは確認できず。[anthropics/claude-code](https://github.com/anthropics/claude-code) の Issue トラッカーを継続監視中）

## 📝 日本語コミュニティ

- 該当なし（Zenn / Qiita で 2026-11-15 付けの新規 Claude Code × iOS 記事は確認できず）

## 🌐 海外コミュニティ / Tips

- [How We Claudified Our iOS App Without Wrecking Our Codebase（tolans.com / Dan Federman）](https://www.tolans.com/relay/how-we-claudified-our-ios-app-without-wrecking-our-codebase) — iOS チームが Claude Code 本格導入から 3 ヶ月で iOS コミットの 55% を Claude 共著にした実践録。「CLAUDE.md よりコードベース自体を整備せよ」という逆転の発想が要点：古いコードを最新テンプレートに合わせることでエージェントが自力で正しいコードを生成するようになった。クラッシュフリー率は 99.6% → 99.9% に向上し、ランタイムエラーは 54% 削減。PR Shepherd（PR 自動番人）エージェントが CI 失敗とレビューコメントに自律的に対応し人間レビューの前にPRを clean 状態にするアーキテクチャも紹介。

- [XcodeBuildMCP v2026 — 増分ビルドを実験的に有効化する方法（mcp.directory）](https://mcp.directory/blog/xcodebuildmcp-complete-guide-2026) — XcodeBuildMCP の増分ビルド（Incremental Builds）はデフォルト無効の実験的機能。`claude mcp add` 時に `INCREMENTAL_BUILDS_ENABLED=true` を env に追加するか `.mcp.json` の env ブロックに設定することで有効化でき、大規模プロジェクトでのビルドループ高速化が見込める（ただし正式サポート前のため本番 CI への適用は慎重に）。

## 💡 今日のおすすめ実践 Tip

**「CLAUDE.md を磨くより、コードベースを整備せよ」— チーム導入の教訓**

tolans.com の "How We Claudified" 記事が示した核心は、エージェントが参照する一次情報はコードベスそのものであるということ。CLAUDE.md に細かいルールを書いても、実際のコードが古いパターン混在のままだとエージェントは古いコードを参考に生成してしまう。

具体的な手順として同チームは次のアプローチを取った：

1. 現行テンプレート（最新の ViewModel・Coordinator・Network 層の書き方）を 1 つの "golden example" ファイルとして整備
2. Claude Code に「このテンプレートに合わせて古いファイルを一括リファクタ」と指示（1 日で 10 万行の均質化）
3. 以降は CLAUDE.md で golden example ファイルへの参照を 1 行書くだけで済む

iOS 開発では特に、Swift 5→6 移行や `async/await` 導入後にパターンが混在しているプロジェクトほど効果が大きい。最初の投資として "コードベースの均質化" を 1 スプリント確保するだけで、その後のエージェント品質が劇的に上がる。
