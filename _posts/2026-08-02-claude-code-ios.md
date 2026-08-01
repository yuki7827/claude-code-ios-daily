---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-08-02"
date: 2026-08-02 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- 新リリースなし（v2.1.220（2026-07-25）が最新。2026-07-26 記事で取り上げ済み）
- **XcodeBuildMCP v2.7.0 リリース（2026-07-23）**（[getsentry/XcodeBuildMCP Releases](https://github.com/getsentry/XcodeBuildMCP/releases)）— Xcode 27 の **Device Hub**（Simulator.app の後継）に UI オートメーションツールが対応。`schemaVersion: "3"` への移行（ビルド・テスト結果の構造変更）と `xcodebuildmcp purge` コマンドの追加が主なポイント。08-01 記事の iOS Simulator MCP クラッシュループ（Issue #82864）とは別レイヤーの改善で、Xcode 27 beta 環境では v2.7.0 への更新が前提になる。本日の Tip で breaking changes 対応手順を紹介する。

## 🛠 GitHub の動き

- [Issue #83174 — Artifact pages（claude.ai/code/artifact/…）が iPhone で閲覧・発見できない（OPEN / 2026-08-01）](https://github.com/anthropics/claude-code/issues/83174) — Claude Code の Artifact ツールで公開したページのリンクを iPhone で開くと、Claude iOS アプリに横取りされてアーティファクトが表示されず、Safari でも別アカウントでサインインした状態だと not-found 画面が出る問題。iOS アプリ内に Code アーティファクトのギャラリー相当のセクションが存在せず、リンクを紛失した場合に手がかりがないという点も指摘されている。**iOS 開発者が UI 仕様書や HTML プロトタイプを Artifact として公開し、外出先で iPhone から確認したいワークフロー**に直接影響する。現時点の回避策は、Artifact URL をブラウザの Safari にダイレクトに貼り付けること（claude.ai にサインインしたブラウザセッションが必要）。

- [Issue #42700 — Remote Control セッションで TTS 読み上げと音声入力が使えるようにしてほしい（OPEN / 最終更新 2026-08-01）](https://github.com/anthropics/claude-code/issues/42700) — `/voice` コマンドは音声入力（STT）のみで、Claude の返答を読み上げる TTS がない。iPhone で Remote Control 接続中に小さなキーボードで入力するのが手間だという要望で、「Claude モバイルアプリにはフル双方向音声があるのに、ライブコードベースにはアクセスできない」という本質的なギャップを突く Issue。Priority が Low に設定されているが、07-31 記事で取り上げた Issue #82586（iOS アプリ閲覧中のバックグラウンドタスク誤 kill）や 07-28 記事の Issue #82374（プッシュ通知不達）など、モバイルからの Claude Code 操作体験に関連する問題が積み重なっている。

## 📝 日本語コミュニティ

- [Claude Code標準SkillをiOSアプリ開発でどう使うか：公式ドキュメントを根拠に整理する（Qiita / 4q_sano）](https://qiita.com/4q_sano/items/0ecf0fc7353d751f601c) — 公式ドキュメントに基づき、`/code-review`（Optional 強制アンラップのリスク検出、`MainActor` / スレッド安全性の確認）など標準 Skill の iOS 固有の使い方を整理した記事。別記事の「VS Code で iOSコードレビュー → 修正 → Xcodeビルド確認 → テスト項目作成を自動化する（[第2弾](https://qiita.com/4q_sano/items/effe51de5e9654777f1e)）」と合わせて読むと、Claude Code を iOS 品質保証フローに組み込む手順が体系的につかめる。

## 🌐 海外コミュニティ / Tips

- [conorluddy/ios-simulator-skill（GitHub、1,100+ stars）](https://github.com/conorluddy/ios-simulator-skill) — Claude Code 用のコミュニティ製 iOS Simulator Skill。`xcodebuild` の出力を token-efficient なフォーマットに変換する `build_and_test.py`、`xcrun simctl` / `idb` 経由のセマンティック UI 操作スクリプト（27 本）、`.xcdatamodeld` と `@Model` Swift 宣言を解析する `model_inspector.py` が含まれる。インストールは `/plugin marketplace add conorluddy/ios-simulator-skill` → `/plugin install ios-simulator-skill@conorluddy` でプラグイン経由が最も手軽。XcodeBuildMCP v2.7.0 の UI オートメーション強化と組み合わせることで、ビルド → 起動 → UI 操作 → ログ収集のループをほぼ自動化できる。

## 💡 今日のおすすめ実践 Tip

**「XcodeBuildMCP v2.7.0 の breaking changes を確認して Xcode 27 Device Hub に対応する」**

XcodeBuildMCP v2.7.0（2026-07-23）は Xcode 27 で Simulator.app が Device Hub に置き換わったことに合わせた大型アップデートです。Xcode 27 beta 環境でそのまま古いバージョンを使い続けると、UI オートメーション系ツールが Device Hub を認識できずに失敗します。

**主な変更点と対処手順**

1. **`schemaVersion: "3"` への移行**  
   ビルド・テスト結果の構造が変わりました。CLAUDE.md や自作スクリプトでレスポンスのフィールドを直接参照している場合はキー名を確認してください。`xctestproducts` パスが結果に含まれるようになり、テスト準備（ビルドのみ）と実行を分離できます。

2. **Device Hub との互換性**  
   v2.7.0 では UI オートメーションツールが Device Hub を直接ターゲットし、ロケール非依存のメニュー操作（英語 OS 以外でも動作）を使うよう修正されています。Simulator.app が存在しない Xcode 27 環境ではこのバージョン以降が必須です。

3. **アップデート手順**  

   ```bash
   # Homebrew の場合
   brew upgrade xcodebuildmcp

   # npm の場合
   npm install -g xcodebuildmcp@latest

   # アップデート確認コマンド（v2.7.0 以降で使える）
   xcodebuildmcp --check
   ```

4. **`xcodebuildmcp purge` コマンドの追加**  
   ワークスペース内のキャッシュストレージを一括削除できる新コマンドです。長期間のプロジェクトでビルドキャッシュが肥大化してきたら積極的に活用してください。

08-01 記事の Issue #82864（iOS Simulator MCP のクラッシュループ）とは独立した問題なので、v2.7.0 に更新しても #82864 が解消するわけではありません。Device Hub での UI スクリーンショットは引き続き XcodeBuildMCP 経由か `xcrun simctl io booted screenshot` で取得するフォールバックを維持することを推奨します。
