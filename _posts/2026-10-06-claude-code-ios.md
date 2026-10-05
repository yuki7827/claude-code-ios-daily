---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-10-06"
date: 2026-10-06 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- **本日時点（2026-10-06）で新バージョンのリリースは確認されていない** — v2.1.289（2026-10-03）が引き続き最新版。詳細は 10-05 の記事参照。なお v2.1.289 で追加された `agent.spawn` の `idle` / `waiting` ステートを使ったマルチエージェント iOS 開発ワークフローについては前回記事で取り上げた。次版の変更内容は [公式 Changelog](https://code.claude.com/docs/en/changelog) をウォッチ推奨。

## 🛠 GitHub の動き

- **[Issue #95191（2026-09-17 作成 / OPEN）— iOS Simulator 入力アクションがサンドボックスの "Failed to initialize IO ports for device IO client" で失敗する](https://github.com/anthropics/claude-code/issues/95191)** — macOS 26.6.2 / Xcode 27.0（Apple Silicon）環境で、Claude Code デスクトップアプリが起動する `claude-ios-sim` ヘルパーの**サンドボックスプロファイル（`claude-ios-sim.sb`）が CoreSimulator IO / indigo の Mach サービスルックアップをブロック**し、`tap` / `swipe` / `text` / `button` といった入力アクションがすべて `SimDeviceIO connectIOPort:` で失敗する。`screenshot` と `attach` は正常に動作する。サンドボックス外で同一コードを実行すると成功するため、API の削除や Xcode 27 の DeviceHub 移行とは無関係な原因。また、アプリ内 Simulator パネルが "success" を報告しつつ表示されないケースや `"iosSimulator": {"status": "unsupported", "reason": "iOS Simulator is disabled by its rollout flag"}` が表示されるケースも同時に報告されている。**修正提案**: `claude-ios-sim.sb` に CoreSimulator IO/indigo Mach サービスの `lookup` 権限を追加することで `SimDeviceIO ioPorts` アクセスが可能になる見込み。ラベル: `bug`, `has repro`, `area:tools`, `platform:macos`。

## 📝 日本語コミュニティ

- **[Xcode MCP×Claude Codeプラグインで、iOSビルドを自動化する（Zenn: kyoichi）](https://zenn.dev/kyoichi/articles/claude-code-plugin-xcode-mcp-hybrid)** — Xcode 26.3 が提供する公式 MCP サーバー（`xcrun mcpbridge`）と Claude Code のプラグイン機能を組み合わせて iOS ビルド自動化を実現する構成を解説。`claude mcp add --transport stdio xcode -s user -- xcrun mcpbridge` で登録した 20 ツールをプラグインから呼び出し、**SwiftUI プレビューのキャプチャ・診断フィードバックの取得・ビルドエラーの自動修正ループ**を Claude Code 内で完結させる実践内容。Xcode が起動していれば追加設定不要で mcpbridge が Xcode の PID を自動検出する点も確認済み。

- **[Claude Codeで5日でiOSアプリをApp Storeに出した全記録 — 何を任せて何を任せちゃダメか（Zenn: seeda_yuto）](https://zenn.dev/seeda_yuto/articles/claude-code-ios-app-5days)** — Swift 経験がほぼゼロの著者が Claude Code だけを使い、SwiftUI + Swift Charts + MapKit（MVVM）構成の iOS アプリを 5 日間（2026-09-29〜2026-10-03）で開発・App Store 審査通過させた全記録。**Claude Code が 45 分でスキャフォールディング 2,763 行を生成**した一方、「Signing ファイル / `Package.resolved` / `*.entitlements` には触らせない」「Simulator への UI 操作は毎回 `xcrun simctl` で確認する」など失敗から得た禁止事項も詳細に記録されている。

## 🌐 海外コミュニティ / Tips

- **[Axiom — モダン Apple プラットフォーム開発向け Claude Code スキル集（GitHub: CharlesWiltgen/Axiom）](https://github.com/CharlesWiltgen/Axiom)** — iOS / iPadOS / watchOS / tvOS（xOS）開発に特化した Claude Code プラグイン。**276 スキル・42 エージェント・17 コマンド**を収録し、Swift 6 の厳格な並行性（actors / Sendable / @MainActor / データレース防止）・SwiftUI Liquid Glass（iOS 26）・Apple Intelligence 統合・Core Data マイグレーション安全性などをカバー。同梱ツール `xcui`（Simulator UI テスト自動化）・`xclog`（コンソールキャプチャ）・`xcsym`（クラッシュシンボリケーション）・`xcprof`（パフォーマンス解析）は Claude Code のツール呼び出しとして統合されており、前述の Sandbox バグによる `claude-ios-sim` 不可時の **`xcui` による入力代替**としても機能する。macOS 27 / Xcode 27 対応（`OS 27 in progress`）で継続メンテ中。インストール: `claude plugin install CharlesWiltgen/Axiom`。

## 💡 今日のおすすめ実践 Tip

**「Axiom の `xcui` を使い、Simulator 入力 Sandbox バグ（Issue #95191）を回避する」**

Issue #95191 で報告されている通り、現時点では Claude Code デスクトップアプリの iOS Simulator パネルで `tap` / `swipe` が機能しないケースがあります。Axiom の `xcui` ツールは Claude Code のプラグインとして動作し、`xcrun simctl io` / `xcrun simctl ui` / AppleScript 経由で入力を注入するため、`claude-ios-sim.sb` のサンドボックス制限を受けません。

```bash
# Axiom のインストール（未インストールの場合）
claude plugin install CharlesWiltgen/Axiom

# Claude Code セッション内で xcui ツールを使った操作例
# 以下は Claude への自然言語指示として入力する
"iPhone 17 Pro シミュレーターの (195, 422) を xcui でタップして"
"ログインボタンをタップ後、スクリーンショットを取得して"
```

`xcui` が内部で使用する `xcrun simctl io booted tap <x> <y>` は `claude-ios-sim.sb` の外で実行されるため、現状の Sandbox バグを回避できます。Issue #95191 が公式修正されるまでの実用的なワークアラウンドとして活用してください。

なお、Issue #95191 の「`iosSimulator` が `"status": "unsupported"` を返す」ケース（ロールアウトフラグで機能が無効化されているケース）には別の原因がある可能性もあります。その場合は `claude --version` でバージョンを確認し、v2.1.289 に更新してから再試行するのが先決です。
