---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-06-15"
date: 2026-06-15 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Claude Code v2.1.177（2026-06-13）](https://github.com/anthropics/claude-code/releases/tag/v2.1.177) — バックグラウンドセッションが、起動デーモン側のシェルから `ANTHROPIC_*` 系のプロバイダ環境変数（ゲートウェイURL・カスタムヘッダー・`/model` エイリアス設定）を誤って継承してしまう不具合を修正。06-11で紹介した「`/cd` でiOSアプリ⇄サーバーを往復する」ようなモノレポ運用での環境変数汚染が解消される。あわせて、米国政府の指示によりClaude Fable 5への全ユーザーアクセスが一時停止された。06-10/06-11で紹介した「Dynamic Workflows + Fable 5でSwift 6移行」のような計画は、当面 `fallbackModel` を `claude-sonnet-4-6` に向けて運用するのが安全。

## 🛠 GitHub の動き

- [dpearson2699/swift-ios-skills](https://github.com/dpearson2699/swift-ios-skills)（★740） — iOS 26+ / Swift 6.3 / SwiftUI向けのAgent Skills集。SwiftData・MapKit・StoreKit・WidgetKit・Liquid Glass・アクセシビリティ・ローカライズなど84本のスキルを収録し、`npx skills add dpearson2699/swift-ios-skills` でClaude Code・Codex・Cursorなど40以上のエージェントに対応したインストールUIが起動する。

- [superagents-lab/xcode27-skills](https://github.com/superagents-lab/xcode27-skills)（★84） — Xcode 27に同梱されたApple公式Agent Skills（`swiftui-specialist`・UIKitモダナイゼーション・Swift Testing・Cの境界安全性・セキュリティ強化など7本）をそのまま再配布するリポジトリ。`npx skills add superagents-lab/xcode27-skills` で導入でき、06-12/06-13で紹介したXcode 27公式スキルのエクスポート手順を踏まずに直接Claude Codeへ取り込める。

## 📝 日本語コミュニティ

該当なし

## 🌐 海外コミュニティ / Tips

- [CharlesWiltgen/Axiom](https://github.com/CharlesWiltgen/Axiom)（[Show HN](https://news.ycombinator.com/item?id=46264591)） — iOS / iPadOS / watchOS / tvOS向けのClaude Code拡張で、254のスキル・40のエージェント・15のコマンドを収録。メモリリーク検出、Swift 6の並行処理違反、ビルド不具合などを自律的にスキャンするエージェント群が特徴で、`/plugin marketplace add CharlesWiltgen/Axiom` で導入できる。`xclog`（シミュレータ/デバイスログ収集）・`xcsym`（クラッシュのシンボリケーション）・`xcprof`（CPU/パフォーマンストレース解析）など実機デバッグ向けのスキルも揃っている。

## 💡 今日のおすすめ実践 Tip

**「実装規約スキルの導入 → Axiomの監査エージェントでレビュー」の2段階を、Fable 5一時停止中はSonnetで回す**

今日紹介した3つのプロジェクトは「最新のiOS 26/Swift 6.3/Xcode 27の作法を教える」役割と「実装後に品質を自律監査する」役割に分かれています。

```bash
# 1. iOS 26 / Swift 6.3 / Apple公式Xcode 27スキルを導入
npx skills add dpearson2699/swift-ios-skills
npx skills add superagents-lab/xcode27-skills

# 2. メモリリーク・並行処理違反・ビルド不具合を監査するAxiomを導入
/plugin marketplace add CharlesWiltgen/Axiom
```

Claude Code への指示例:

```
1. swift-ios-skills と xcode27-skills を使って、新機能のSwiftUI実装を
   iOS 26 / Swift 6.3 の作法に沿って進めて。

2. 実装が終わったら Axiom の監査エージェントで、メモリリークや
   Swift 6 strict concurrency違反、ビルド時の警告がないかスキャンして。

3. v2.1.177 でFable 5へのアクセスが一時停止されているため、
   大規模なSwift 6移行のような重いタスクは
   fallbackModel に claude-sonnet-4-6 を指定したまま実行して。
```

Fable 5が使えない期間は、06-10で紹介したDynamic Workflowsのような大規模並列タスクほどモデル切り替えの影響を受けやすいため、`.claude/settings.json` の `fallbackModel` 設定を見直しておくと、再開時にスムーズに移行できます。
