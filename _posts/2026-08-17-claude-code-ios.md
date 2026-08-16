---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-08-17"
date: 2026-08-17 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- 該当なし（2026-08-14 リリースの v2.1.233 が最新版。2026-08-15〜17 の期間で新規リリースは確認されていない）

## 🛠 GitHub の動き

- [Issue #86994 — iOS Simulator ツールが xcode-select・Developer Mode・Developer Tools パーミッション設定済みでも毎回 "Xcode not selected" を返す（OPEN / 2026-08-15）](https://github.com/anthropics/claude-code/issues/86994) — 2026-08-15 に起票された新規 Issue。環境は macOS 26.5.2 (Build 25F84) + Xcode 26.6 (Build 17F113) + Claude Code 2.1.185。**Metal サンドボックス問題（08-16 記事で報告した Issue 群の一斉クローズ）とは別系統の問題**で、① `xcode-select -p` が正しいパスを返す、② `DevToolsSecurity -status` で Developer Mode が有効、③ Claude.app が Developer Tools パーミッション一覧に表示・有効済み、の三条件を満たしているにもかかわらず、`attach` アクションが毎回バイト単位で同一の "Xcode not selected" エラーを返し続ける。エラーの内容が再起動を繰り返しても変化しないため、報告者はキャッシュされた古い状態を読み取っているか、あるいはサンドボックス内の別パスを参照している可能性を指摘している。Issue #80041（`/var/db/xcode_select_link` シンボリックリンクの有無が原因）および Issue #84943（Xcode 26.6 / macOS 26.5 の同様の症状）と同根の可能性が高い。**現時点でユーザー側での回避策は提示されていない。**

## 📝 日本語コミュニティ

- 該当なし

## 🌐 海外コミュニティ / Tips

- [I Tried Claude Agent SDK in Xcode 26.3 for a Week. My iOS Dev Workflow Will Never Be the Same.（Medium / Iniyarajan S. / Aug 2026）](https://medium.com/@iniyarajan/i-tried-claude-agent-sdk-in-xcode-26-3-for-a-week-my-ios-dev-workflow-will-never-be-the-same-cc899ed7a776) — Xcode 26.3 に統合された Claude Agent SDK を 1 週間使い込んだ iOS 開発者のレポート。サブエージェント・バックグラウンドタスク・プラグインを Xcode を離れずフルに使える点と、エージェントが `xcrun simctl` を自律的に呼び出しコンパイルエラーを修正しながらビルドを繰り返すループについての実践的な知見が含まれる。

- [swiftui-claude-skills（199-biotechnologies / GitHub）](https://github.com/199-biotechnologies/swiftui-claude-skills) — AI スウォームで検証済みの Claude Code スキル集。iOS 26 の Liquid Glass（`glassEffect()`・`GlassEffectContainer`・Glass Morphing・ボタンスタイル）、SwiftUI アニメーション（Spring プリセット・ナビゲーションパターン）、On-Device AI（Foundation Models フレームワーク・構造化出力）の 4 カテゴリのカスタムスキルに加え、Paul Hudson 監修の SwiftUI コードレビュー・Swift Testing・Swift 6.2 並行処理・SwiftData の Pro スキルをバンドル。**Apple 公式ドキュメントと照合して存在しない API（Glass バリアントの誤称など）を排除した点が特徴**で、iOS 26.4 RC / macOS 26.4 RC 時点の最新 API に対応している（最終更新 2026-03-18）。全投稿での初掲載。

## 💡 今日のおすすめ実践 Tip

**「iOS Simulator パネルが "Xcode not selected" を返す場合の診断フロー — `/var/db/xcode_select_link` が原因のケースを見分ける」**

Issue #86994（本日起票）のように、xcode-select が正しく設定されているにもかかわらず Claude Code Desktop の iOS Simulator パネルが "Xcode not selected" を返し続ける問題は、macOS 26.x 環境で発生報告が相次いでいます。根本原因が複数あるため、以下の診断フローで切り分けが有効です。

**ステップ 1: xcode-select の状態を確認**

```bash
xcode-select -p
# 期待値: /Applications/Xcode.app/Contents/Developer
```

このコマンドが正しいパスを返しているにもかかわらずエラーが出る場合は、ステップ 2 へ。

**ステップ 2: `/var/db/xcode_select_link` シンボリックリンクを確認**

macOS 26 では、インストール方法によってこのシンボリックリンクが作成されないケースがあります（Issue #80041 の根本原因）。Claude Code がこのリンクを優先参照している場合、`xcode-select` の実際の設定を見ずにエラーを返します。

```bash
ls -la /var/db/xcode_select_link
# 存在しない場合 → ステップ 3 へ
```

**ステップ 3: シンボリックリンクを手動で再作成**

```bash
sudo ln -sfn "$(xcode-select -p)" /var/db/xcode_select_link
# その後 Claude Desktop を再起動
```

これで解決する場合は Issue #80041 のケース。解決しない場合は Issue #86994 の別の根本原因（キャッシュや別パス参照）の可能性があり、引き続き Issue を追跡してください。

**XcodeBuildMCP の `build` アクションとの使い分け**

Panel の `attach`・`launch` が利用できない間も、XcodeBuildMCP の `build` ツールはサンドボックスプロファイルに依存しないため影響を受けません。Claude Code に依頼する際に「iOS Simulator パネルではなく XcodeBuildMCP 経由でビルドして」と明示することで、パネルの問題を回避しながら開発を継続できます。

```
# CLAUDE.md への追記例（iOS Simulator パネルが使えない環境用）
## ビルド・テストのデフォルト手段
iOS Simulator パネル (attach/launch) ではなく、XcodeBuildMCP の build ツールを使ってください。
パネルの attach が "Xcode not selected" を返す既知のバグ (#86994) があるためです。
```
