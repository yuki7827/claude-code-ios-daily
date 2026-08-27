---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-08-28"
date: 2026-08-28 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- **v2.1.247（2026-08-26）リリース**（[Claude Code 変更ログ](https://code.claude.com/docs/en/changelog)）— iOS 開発観点での主な変更点: ① **`SendFeedback` ツールを追加** — セッション中に何か問題が起きたとき Claude がフィードバックレポートの下書きを作成し、`/feedback` から確認・送信できる機能が追加された（`feedbackDrafts` 設定で無効化可能）。iOS 開発中に発生したエージェント挙動の不具合を Anthropic に報告するハードルが大きく下がる; ② **`/claude-api cost-optimize` コマンドを追加** — API 利用コストのプロファイリングと最適化提案が `/claude-api` スキル経由で実行できるようになった。XcodeBuildMCP や iOS CI ワークフローで Claude Code を大量実行しているチームのコスト可視化に有用; ③ **スピナーヒントに組織カスタム Tips のローテーションを追加** — `settings.json` でチーム独自のヒントを差し込めるようになった; ④ **フルスクリーンレンダラーと Remote Control セッションの各種バグ修正**

## 🛠 GitHub の動き

- [Issue #88833 — 「open simulator」1 リクエストに 6 回のやりとり・20 回超のツール呼び出しを要したエージェント推論失敗の詳細レポート（OPEN / 2026-08-22 起票）](https://github.com/anthropics/claude-code/issues/88833) — macOS 15.3 + Xcode 26.6 + iOS 26.5 シミュレータ環境での実体験レポート。「open simulator」という 1 行のリクエストに対してエージェントが以下の失敗を連続させた: ①ゴールでなくシェルコマンドとして解釈し `open -a Simulator` を実行して「完了」と報告、②「それは最新バージョンではない」という返答に対して Xcode SDK・ダウンロード可能ランタイム等を調査（アプリバージョンを指していた）、③`CODE_SIGNING_ALLOWED=NO` でビルドしたためキーチェーン呼び出しが `-34018` エラーで全失敗し認証トークンが保存されず空ダッシュボードになった、④2 か月前のコンテナの上にインストールし古いセッションが復元された、⑤自分で調査できる問いをユーザーに委ねた、⑥実際のデータ確認なしに「修正完了」を 2 度宣言。**iOS 開発者が Claude Code に「open simulator」と指示する際、「アプリが起動し・サインイン済みで・データが表示された状態」まで目標を具体化しないと同じ問題が起きる**という実務的な教訓が詳細に記録されている。

- [Issue #80142 — sidecar の startVideo が 10 秒後に常時タイムアウト→ iOS Simulator パネルが無限クラッシュループに陥る（OPEN）](https://github.com/anthropics/claude-code/issues/80142) — macOS 26.5.2 + Xcode 26.5 + iOS 26.3 ランタイム環境で、コントロールプレーン（attach/tap/screenshot）は正常動作するが映像ストリーミングの `startVideo` が 10 秒で必ずタイムアウトし、パネルが「restarting after a crash」の無限ループに入るバグ。ブラウザ側ログでは `[SimulatorH264Canvas] stream HTTP 503` が記録されており、ヘルパープロセス `claude-ios-sim` の映像プレーンが一度も起動しない。再起動・デバイス切り替えでも解消しない。コントロールプレーンが正常なためスクリーンショット・タップ等は MCP 経由で引き続き利用可能。

## 📝 日本語コミュニティ

- [週末アプリ開発者のための、Claude Code × iOSアプリ開発 一気通貫ガイド（Qiita / tobaru-hideyasu）](https://qiita.com/tobaru-hideyasu/items/f9ed60b136d95960b1d0) — 週末開発者を対象に、アイデア出しから App Store 申請まで Claude Code を軸にしたワークフローをまとめたガイド記事。PRD 設計・SwiftUI 実装・XcodeBuildMCP によるビルド自動化・TestFlight 提出の流れを一本道で解説。初出。

- [Claude Codeのセットアップとモバイルアプリの環境構築（Zenn / solar）](https://zenn.dev/solar/articles/6a2c9ffaf9fe16) — iPhone 16 Pro / iOS 18.5 シミュレータの初期セットアップ手順と、Claude Code プロジェクト向けの Xcode ビルドコマンド群を整理した入門記事。環境構築でつまずきやすいポイントをステップ形式でカバーしている。初出。

## 🌐 海外コミュニティ / Tips

- [XcodeBuildMCP — 新機能: platform-aware セットアップウィザード・in-place アップグレード・シェルインジェクション修正（getsentry/XcodeBuildMCP）](https://github.com/getsentry/XcodeBuildMCP) — 直近リリースで以下の機能が追加された: ① `xcodebuildmcp setup` ウィザードがプラットフォーム（macOS / iOS / tvOS / watchOS / visionOS）を先に選択し、プラットフォームに適したワークフロー推奨を表示する形に刷新; ② `xcodebuildmcp upgrade` コマンドでその場でアップグレード確認・実行が可能になった（`--check` で確認のみ、`--yes/-y` で非インタラクティブ実行）; ③ 環境変数由来の値がシェルコマンドとして解釈されるセキュリティリスクを修正。`npm install -g xcodebuildmcp@latest` で最新版に更新後、`xcodebuildmcp setup` を実行して iOS ワークフロー設定を見直すことを推奨。

- [The Complete Guide to Building an iOS App with Claude Code (No Xcode Required)（DEV Community / marypiakovski）](https://dev.to/marypiakovski/the-complete-guide-to-building-an-ios-app-with-claude-code-no-xcode-required-o5b) — XcodeBuildMCP + ios-simulator-skill + AXe の組み合わせで、Xcode を開かずにターミナルから Claude Code だけで iOS アプリをビルド・シミュレータで動作確認する完全ガイド。エージェントがエラー→修正→ビルドのループを自律的に回す「No Xcode UI」ワークフローの実用性と限界点（`.pbxproj` 操作・コード署名・ビジュアルデバッグ）を正直に評価している。

## 💡 今日のおすすめ実践 Tip

**「Issue #88833 から学ぶ：Claude Code への iOS シミュレータ指示は〈完了状態〉まで書く」**

Issue #88833 は、シンプルな「open simulator」という指示がいかに曖昧かを 20 回超のツール呼び出しで実証しました。エージェントは「Simulator.app を起動したか」で完了を判断し、「アプリが動いてデータが表示されているか」まで確認しませんでした。

**問題のある指示 vs. 改善後の指示**

```
# ❌ 曖昧（Simulator.app の起動で完了とみなされる）
"open simulator"

# ✅ 完了状態まで明示（エージェントが自律的に検証できる）
"iPhone 16 Pro シミュレータを起動してアプリをビルド・インストールし、
 ダッシュボード画面に実データが表示された状態にしてください。
 ログイン・API 呼び出しが成功していることも確認してください。"
```

**`CODE_SIGNING_ALLOWED=NO` を自動使用させない**

Issue #88833 ではエージェントが「コンパイル確認用」の習慣でこのフラグを付与し、キーチェーン呼び出しを全滅させました。CLAUDE.md に以下を追記することで防げます:

```markdown
## シミュレータビルドの制約

シミュレータ向けビルドでは `CODE_SIGNING_ALLOWED=NO` を**絶対に使わないこと**。
キーチェーン・AuthKit・StoreKit が使用不能になり、認証・課金フローが壊れる。
ビルドコマンド例:
xcodebuild -scheme MyApp -destination "platform=iOS Simulator,name=iPhone 16 Pro" build
```

**古いコンテナを毎回クリーンにする**

2 か月前の古いコンテナが原因で誤診断が続いた教訓から、シミュレータ起動タスクの前に明示的なコンテナ削除を CLAUDE.md または hooks に組み込んでおくと安全です:

```bash
# シミュレータ起動前に旧コンテナを削除するフック例
# .claude/hooks/pre-task.sh
xcrun simctl uninstall booted com.example.MyApp 2>/dev/null || true
```

エージェントへの指示は「何をするか」ではなく「何が完了したら成功か」を中心に書く習慣が、Issue #88833 のような連鎖的な失敗を予防します。
