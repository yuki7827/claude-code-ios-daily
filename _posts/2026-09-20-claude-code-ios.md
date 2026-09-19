---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-09-20"
date: 2026-09-20 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- **[v2.1.278（2026-09-19）リリース](https://code.claude.com/docs/en/changelog)** — **Auto mode がサーバーサイド分類器をデフォルト使用**（追加のオーバーヘッド料金なし）。`/status` に「Auto mode server」行が追加され、サーバー側で分類器が動いているかを確認できる。合わせて **CLAUDE.md が存在しないプロジェクトでは AGENTS.md を自動的にフォールバックとして読み込む**（`/config` 「Project instructions」から変更可能。Bedrock・Vertex・Foundry では未対応）。gateway upstream 設定に `headers:` マップが追加され、静的ヘッダーをプロキシに送信可能に。バグ修正多数（テキストコンテンツブロックの空文字エラー、resume 時の複数会話問題、plugin install 周辺、PDF・ファイル編集・サンドボックス実行など）。

## 🛠 GitHub の動き

- **[Issue #95466（2026-09-18 作成 / OPEN）— iOS Simulator ツール: Xcode 27 アップグレード後に tap/swipe 注入がサイレントに no-op になる（Simulator.app が headless DeviceHub.app に置き換えられた）](https://github.com/anthropics/claude-code/issues/95466)** — macOS 27.0 / Xcode 27.0 環境で `tap` / `swipe` がエラーなく成功を返しながら実際には UI に何の変化も生じない。`screenshot`（スクリーンキャプチャ）とハードウェアボタン（Home）操作は正常動作するが、タッチ/ジェスチャー注入だけが完全に沈黙する。根本原因の仮説: Xcode 27 が `Simulator.app`（GUI）を廃止し、画面を持たない `DeviceHub.app` エージェントプロセスに置き換えたため、旧アーキテクチャを前提としていたタッチ注入 API パスが機能しなくなった。3 機種×2 iOS ランタイムで再現確認済み。**暫定回避策**: スクリーンショット確認は `xcrun simctl io <UDID> screenshot`、ナビゲーションは `xcrun simctl openurl` / `xcrun simctl launch` / deep link を活用する。

- **[Issue #95191（2026-09-17 作成 / OPEN）— iOS Simulator tap 等の入力操作がサンドボックスにより失敗: "Failed to initialize IO ports for device IO client"（macOS 26.6.2 + Xcode 27）](https://github.com/anthropics/claude-code/issues/95191)** — macOS 26.6.2（安定版）+ Xcode 27.0 の環境で `tap` / `swipe` / `text` / `button` が `clientCreationFailed … "Failed to initialize IO ports for device IO client"` で失敗。`attach` / `launch` / `screenshot` は正常動作。報告者が同じ booted simulator に対して**サンドボックスなし**の Swift バイナリから `SimDeviceLegacyHIDClient` を初期化すると成功することを確認しており、`claude-ios-sim.sb` サンドボックスが CoreSimulator の IO/indigo Mach サービスへのアクセスを拒否していることが原因と特定。**提案された修正**: `claude-ios-sim.sb` に CoreSimulator IO ポート向け Mach サービスを許可するエントリを追加。macOS 27 系の問題ではなく安定版 26.x でも発生するため、Xcode 27 へアップグレードした全環境が対象。

## 📝 日本語コミュニティ

- **[Claude Code標準SkillをiOSアプリ開発でどう使うか：公式ドキュメントを根拠に整理する（Qiita / 4q_sano 氏）](https://qiita.com/4q_sano/items/0ecf0fc7353d751f601c)** — Claude Code の標準スキル（`/build`・`/test`・`/review`・`/deploy` 相当のもの）を iOS アプリ開発で実際にどう活用するかを公式ドキュメント準拠でまとめた記事。xcodebuild ラップ・TestFlight 配布・App Store Connect 連携の文脈でスキルを組み合わせるユースケースを紹介。第1〜3弾シリーズの補足として読むのに適した内容。

## 🌐 海外コミュニティ / Tips

- **[Xcode 26.3 + Claude Agent — Model Swapping, MCP, Skills, and Adaptive Configuration（fatbobman.com）](https://fatbobman.com/en/posts/xcode-263-claude/)** — Xcode 26.3 組み込みの Claude Agent において、`settings.json` / `config.toml` でモデルを切り替える方法（Bedrock・Vertex・Foundry 含む）、MCP サーバーとスキルのセットアップ、プロジェクトごとの適応的設定の書き方を網羅した英語ガイド。日本語コミュニティでは treastrain 氏が同様の内容を Zenn でまとめているが、英語で体系的に読みたい場合の参考として有用。

## 💡 今日のおすすめ実践 Tip

**「Xcode 27 環境での iOS Simulator タッチ注入障害を回避する 3 つの代替手段（Issue #95191 / #95466 暫定対応）」**

Xcode 27 への移行により、iOS Simulator ツールの tap / swipe が **2 つの独立した理由**でほぼ全環境で動かなくなっています。

1. **DeviceHub 移行（macOS 27）**: `Simulator.app` 廃止によりタッチ注入 API パスが壊れた（#95466）
2. **Sandbox 制限（macOS 26.6.2 + Xcode 27）**: `claude-ios-sim.sb` が CoreSimulator IO Mach サービスへのアクセスを拒否（#95191）

どちらも現時点でユーザーが直接修正できる問題ではありませんが、以下の代替手段で iOS 開発ループを維持できます。

**代替手段 ① `xcrun simctl` で起動・URL・スクリーンショットをカバー**

```bash
# アプリ起動（tap の代わりにアプリ直接起動）
xcrun simctl launch booted com.example.MyApp

# ディープリンクでナビゲーション
xcrun simctl openurl booted "myapp://home"

# スクリーンショット取得（Claude パネル代替）
xcrun simctl io booted screenshot /tmp/sim.png
```

**代替手段 ② XcodeBuildMCP の `control` ツールを使う（サンドボックス外で動作）**

XcodeBuildMCP は独自プロセスとして動作するため、`claude-ios-sim.sb` の制約を受けません。tap 操作が必要な場合は MCP 経由で実行できます。

**代替手段 ③ CLAUDE.md に「tap 不可」の制約を明示して迷走を防ぐ**

```markdown
## iOS Simulator 制約（Xcode 27 + 2026-09 時点）
- `tap` / `swipe` は現在 Xcode 27 環境で動作しない（Issue #95191 / #95466）
- 代替: xcrun simctl launch / openurl / screenshot を使うこと
- スクリーンショット確認: xcrun simctl io <UDID> screenshot /tmp/sim.png
- UI 操作が必須な場合: XcodeBuildMCP の control ツールを試す
```

Anthropic 側での修正が入るまでの間、CLAUDE.md にこの制約を書いておくことで Claude がタップ試行を繰り返して開発ループを止めるシナリオを防げます。
