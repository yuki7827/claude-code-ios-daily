---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-08-08"
date: 2026-08-08 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- **v2.1.224（2026-08-07）リリース**（[Claude Code 変更ログ](https://code.claude.com/docs/en/changelog)）— iOS 開発チームへの影響が大きい主要変更: ① **Self-hosted environments がパブリックベータ公開**（Team / Enterprise プラン）— `claude self-hosted-runner` コマンドで自社のマシンやコンテナ上に Cloud Session を誘導できるようになった。XcodeBuildMCP のように macOS でしか動かないツールを使う iOS 開発チームにとっては「チーム全員がブラウザ・iPhone から操作しつつ、実際のビルドは社内 Mac ランナー上で動く」構成が可能になる（詳細は本日 Tip を参照）; ② **Remote Control のコンパクション進捗表示改善** — Web / モバイルクライアントがコンパクション進行状況とコンパクション後の境界を確認できるようになった。長時間 XcodeBuildMCP セッション中に iPhone から状態確認する際に役立つ; ③ **Cross-session SendMessage（macOS / Linux）** — セッション間でのメッセージ送受信設定が追加され、バイパスパーミッションで実行中のセッション宛クロスセッションメッセージは承認待ちで保留、その他のセッション宛は自動配信となった。

## 🛠 GitHub の動き

- [Issue #81520 — Desktop アプリ iOS Simulator パネルが macOS 27 beta で BitmapStream SIGABRT によりクラッシュループ（OPEN / 2026-07-27）](https://github.com/anthropics/claude-code/issues/81520) — macOS 27 beta 上で Claude Desktop の iOS Simulator パネルを起動すると `claude-ios-sim` ヘルパーが SIGABRT でクラッシュし、再起動→即クラッシュを繰り返す問題。**2 件の独立した不具合が重なっている。** Bug 1（SimulatorKit パス変更）: Xcode 27 beta 4 が `SimulatorKit.framework` を `Contents/Developer/Library/PrivateFrameworks/` から `Contents/SharedFrameworks/` に移動したことで起動失敗。**回避策: 旧パスにシンボリックリンクを作成** → `mkdir -p "/Applications/Xcode-27.0.0-Beta.4.app/Contents/Developer/Library/PrivateFrameworks" && ln -s ../../../SharedFrameworks/SimulatorKit.framework "/Applications/Xcode-27.0.0-Beta.4.app/Contents/Developer/Library/PrivateFrameworks/SimulatorKit.framework"`。Bug 2（BitmapStream クラッシュ）: Bug 1 を回避した後も CIContext / Metal 初期化チェーン（`CI::MetalContext::init`）で SIGABRT が発生し Simulator パネルが利用不能になる。**回避策なし。** build / launch ツールは引き続き動作するためビルド自体には影響しない。macOS 27 beta + Xcode 27 + Claude Desktop で iOS Simulator パネルを使っている場合は現時点でパネル経由のスクリーンショット確認はできない。

## 📝 日本語コミュニティ

- [Xcode 26.3 エージェント型コーディング入門 — Claude・Codex・MCP で開発を自動化（Qiita / kai_kou）](https://qiita.com/kai_kou/items/1b24ad62dde4c02ae4f0) — Xcode 26.3 でネイティブ統合された Claude Agent と Codex の両方を実際に試しながら iOS アプリを自動生成する手順を解説した入門記事。Claude Agent SDK と MCP 接続の設定方法、SwiftUI コンポーネント生成、ビルドエラーの自動修正フローが具体例付きで紹介されている。これから Xcode の AI エージェント機能を使い始める開発者向けの出発点として適している。

## 🌐 海外コミュニティ / Tips

- **XcodeBuildMCP v2.7.0（2026-07-23）** — [GitHub Releases](https://github.com/getsentry/XcodeBuildMCP/releases) / [公式 Changelog](https://www.xcodebuildmcp.com/docs/changelog) — 今週のビルドで影響しうる主要変更: ① **Xcode 27 Device Hub に対応した UI オートメーション** — Device Hub ベースのシミュレーターで UI 操作ツール（タップ・スクロール等）が動作するようになった（Xcode 26 系以前との後方互換あり）; ② **`xcodebuildmcp purge` コマンド追加** — ワークスペースストレージの検査とクリーンアップが 1 コマンドで可能に。長期プロジェクトで派生したキャッシュ容量を把握・解放するのに使える; ③ **`extraArgs` セッションデフォルト** — 毎回のビルド/テストで共通フラグ（`-parallelizeTargets`, `-configuration AdHoc` 等）を `extraArgs` セッション変数に登録しておくと、以後のすべての xcodebuild 呼び出しに自動付与される。CLAUDE.md の `extraArgs` セクションで管理すれば Claude Code が引き継ぐ; ④ **ビルド結果スキーマが v3 に更新**（破壊的変更）— 構造化出力バリデーターを自作している場合はスキーマ v2 から v3 への更新が必要。

## 💡 今日のおすすめ実践 Tip

**「Self-hosted environments × XcodeBuildMCP で iOS 開発チーム用 Mac ランナーを構築する（v2.1.224 新機能）」**

v2.1.224 でパブリックベータになった **Self-hosted environments** は、「XcodeBuildMCP はローカル Mac でしか動かない」という iOS 開発チームの制約を解消する可能性を持っています。構成のイメージは以下の通りです。

**構成イメージ**

```
開発者 (iPhone / ブラウザ) → Cloud Session → 自社 Mac ランナー (XcodeBuildMCP + Xcode 入り)
                                                ↕
                                        GitHub (プライベートリポ)
```

1. macOS 14.5+ / Xcode 26.x 以降がインストールされた常時起動 Mac を用意する
2. XcodeBuildMCP を brew でインストール: `brew tap getsentry/xcodebuildmcp && brew install xcodebuildmcp`
3. `claude self-hosted-runner --environment <env-id> --capacity 2` で Mac をランナーとして登録
4. チームメンバーが claude.ai / iPhone からセッションを起動し、environment ピッカーで自社 Mac 環境を選択

**iOS 開発で特に効くポイント**

- **XcodeBuildMCP の「macOS 縛り」を解消** — クラウドランナー（Linux コンテナ）では動かない XcodeBuildMCP が、Mac ランナー上なら当然動く
- **コードベースが社内に留まる** — プライベートリポのソースコードとビルドアーティファクトが自社 Mac から外に出ない。GitHub の内部アクセスが必要な依存 SPM パッケージも解決できる
- **iPhone から始動してそのまま Mac でビルド** — Remote Control のコンパクション進捗表示改善（v2.1.224）と合わせて、iPhone でプロンプトを送り → Mac でビルド → 結果を iPhone で確認 というループが一段と快適になる

**注意点**

Self-hosted environments は Team / Enterprise プランのみ、かつ管理者が claude.ai 管理設定の「Allow self-hosted environments」を有効にする必要があります（デフォルト off）。また、現時点では Zero Data Retention 有効組織では利用不可。詳細は [公式ドキュメント](https://code.claude.com/docs/en/self-hosted-environments) を参照してください。
