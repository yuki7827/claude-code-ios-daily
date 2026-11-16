---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-11-17"
date: 2026-11-17 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- 該当なし（本日時点で v2.1.325 以降の新規リリースは確認できず。[公式 Changelog](https://code.claude.com/docs/en/changelog) を継続確認中）

## 🛠 GitHub の動き

- [ios-simulator-skill v1.5+ — Xcode 27 / DeviceHub.app 対応（conorluddy/ios-simulator-skill）](https://github.com/conorluddy/ios-simulator-skill) — Xcode 27 では `Simulator.app` が `DeviceHub.app` に置き換わり `open -a Simulator` が失敗するようになったため、同スキルが対応を完了。また `idb-companion` が Homebrew core から削除されたため Meta 公式 tap からのインストールに切り替えが必要（1.5.1 以上が必須）。**Duo（折りたたみ iPhone）**のヒンジ状態・向き・アクティブディスプレイを取得できる `duo.py` も追加されており、折りたたみ端末でのレイアウトテストをエージェントに任せやすくなった。31 本のスクリプトがあり、`xcodebuild` と `xcrun simctl` / `idb` を直接ラップしてトークン消費を抑える設計。

- [greenstevester/fastlane-skill — TestFlight・App Store 申請を自然言語で（GitHub）](https://github.com/greenstevester/fastlane-skill) — Claude Code のプラグインとして `/plugin marketplace add greenstevester/fastlane-skill` 1 コマンドで導入可能な Fastlane ラッパースキル。「TestFlight にアップロードして」「App Store にリリースして」という自然言語の指示に応じ、`fastlane ios beta` や `fastlane ios release` を実行してくれる。コードサイニングには `match` サブスキルも同梱されており、証明書エラーを Claude Code に渡すとそのまま `match` で解決しようとしてくれる。`.xcodeproj` または `.xcworkspace` があるディレクトリからの実行が前提。

## 📝 日本語コミュニティ

- [Muse × Claude Code で、絵を描かずに動くアバターを iOS アプリに載せた（Zenn / stak22）](https://zenn.dev/stak22/articles/4eae6e28ba5f29) — 画像生成 AI「Muse」で描いた 1 枚絵に Claude Code でメッシュリグを生成し、WKWebView 経由で SwiftUI アプリに組み込んだ実践報告（2026/11/03）。まばたき・首の傾き・呼吸・髪の揺れを `#if DEBUG` フラグなしで本番に載せるまでの手順が詳述されている。外部通信をコンテンツルールで遮断しつつ独自スキームで同梱ファイルのみで動かすアーキテクチャも参考になる。

- [Claude Code にプロダクションコードを全チェックさせたら、金銭バグ 4 件を含む 15 件の不具合を自動修正してくれた話（Qiita / sorabcjanne1）](https://qiita.com/sorabcjanne1/items/8f3a354ff073ed69aac2) — サロン向け予約アプリ（iOS: Swift 68 ファイル + Node.js バックエンド）を Claude Code で並列レビュー。Stripe ポーリングの排他制御欠如による二重課金・`ReservationStatus` enum の不完全な case 定義・通知再登録漏れなど iOS/Swift 固有のバグが摘出され、iOS テスト 35 件がすべて通過するまで自動修正が継続された記録。複数エージェントで並列レビューしてメインにサマリーを返す分業パターンが実例で示されている。

## 🌐 海外コミュニティ / Tips

- [Claude Code brings live iOS app testing into its Mac app（9to5mac）](https://9to5mac.com/2026/07/21/claude-code-brings-live-ios-app-testing-into-its-mac-app/) — Claude Code Desktop（macOS）に **iOS Simulator ペイン**が搭載された（2026/07/21、パブリックベータ）。Pro / Max / Team プランで利用可能。会話欄の横にシミュレーターペインが開き、Claude がアプリの動作を見ながらタップ・スワイプして自ら変更を検証できる。クラウドセッションや SSH セッションからは使えない（macOS ローカルセッション限定）。前提として Claude Desktop v1.24012.0 以降と iOS プラットフォームを含む Xcode が必要。

## 💡 今日のおすすめ実践 Tip

**fastlane-skill で TestFlight アップロードを Claude Code に任せる最小セットアップ**

```bash
# 1. プラグインをインストール（Claude Code 再起動が必要）
/plugin marketplace add greenstevester/fastlane-skill

# 2. fastlane を初期化（.xcodeproj があるディレクトリで）
# Claude Code に「fastlane をセットアップして」と指示するだけで
# Appfile / Fastfile（test / beta / release レーン付き）が生成される

# 3. TestFlight にアップロード
# 「TestFlight にビルドをアップロードして」と指示すると
# fastlane ios beta が自動実行される
```

注意点として、コードサイニングエラーが発生した場合は「match でサイニングをセットアップして」と続けて伝えると `match` サブスキルが走る。ただし App Store Connect の API キーは事前に環境変数（`APP_STORE_CONNECT_API_KEY_PATH` 等）に設定しておく必要がある。CI/CD での無人実行時は `permissions.allow` に `Skill(setup-fastlane)` と `Skill(beta)` を追加して確認プロンプトをスキップすること（v2.1.325 以降のスキル実行ポリシー変更に対応）。
