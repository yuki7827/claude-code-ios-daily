---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-07-26"
date: 2026-07-26 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Claude Code v2.1.220（2026-07-25）](https://github.com/anthropics/claude-code/releases/tag/v2.1.220) — v2.1.219 のリリースから約 19 時間後に出たパッチリリース。変更内容は「バグ修正と安定性改善」のみで詳細は非公開。前日の v2.1.219 で追加された `DirectoryAdded` フックや `sandbox.network.strictAllowlist` に関連する修正が含まれている可能性がある。iOS 開発者は通常通りアップデートを適用しておくだけで十分。

## 🛠 GitHub の動き

- [XcodeBuildMCP v2.7.0（2026-07-23）](https://github.com/getsentry/XcodeBuildMCP/releases/tag/v2.7.0) — **Xcode 27 の DeviceHub シミュレーター対応**をはじめとする大型アップデート。7/24 の記事で取り上げた Issue #80425（Xcode 27 で `attach` が「No booted simulator found」を返す問題）が XcodeBuildMCP 側で先行解決。主な変更点は以下のとおり。**① DeviceHub UI 自動化**：Xcode 27 が `Simulator.app` を `DeviceHub.app` に移行した変更に追従し、シミュレーターウィンドウ起動・キーボード操作などの UI 自動化ツールが再び動作する。**② ビルド/テスト出力スキーマ v3**：`schemaVersion: 3` に更新され、`testCases` リストに各テストの実行時間が含まれるようになった。スキーマ v2 を前提としたバリデーターは修正が必要。**③ 未指定ビルド構成の挙動変更（破壊的変更）**：`--configuration` 省略時にデフォルトの Debug ではなくスキームの設定を優先するようになった。既存の CI スクリプトで挙動が変わる可能性がある。**④ `xcodebuildmcp purge` コマンド追加**：ワークスペースのストレージを検査・クリーンアップするコマンド。`--dry-run` オプションで実際に削除する前に確認できる。**⑤ MCP クライアント接続遅延を修正**：初回接続に 10〜17 秒かかっていたバグを解消。**⑥ セキュリティ修正**：環境変数のハンドリングに関する脆弱性を複数修正。Xcode 27 beta を使用中の場合は早急なアップデートを推奨。

- [ClaudeForFoundationModels v0.1.4（2026-07-24）](https://github.com/anthropics/ClaudeForFoundationModels/releases) — Anthropic の公式 Swift Package に `claude-opus-5` モデルが追加された（PR #21）。本パッケージは WWDC 2026 で公開された Apache-2.0 ライセンスのライブラリで、Apple の `LanguageModel` プロトコル（iOS 27 / Xcode 27 beta 以降）に準拠した形で Claude を iOS / macOS アプリから直接呼び出せる。Apple オンデバイスモデルと同じ `LanguageModelSession` API を使うため、モデルプロバイダーを引数一つで差し替えられる設計。`claude-opus-5` の追加により、1M トークンのコンテキストウィンドウを持つモデルがアプリ内から利用可能になった。大規模な Xcode プロジェクトの解析やコード補完用途に適している。

## 📝 日本語コミュニティ

- [Claude / Anthropic 関連ニュースまとめ（2026-07-25）（Qiita / homhom44）](https://qiita.com/homhom44/items/c966dc8418658dc5c295) — 2026-07-25 分の Claude / Anthropic 関連ニュースをコンパクトにまとめた記事。v2.1.219 の変更点と iOS 関連の最新 Issue を概要レベルでカバーしており、週次でトレンドを追いたい場合に便利。

## 🌐 海外コミュニティ / Tips

該当なし

## 💡 今日のおすすめ実践 Tip

**「XcodeBuildMCP v2.7.0 へのアップデート手順と、DeviceHub 対応・スキーマ v3 の確認ポイント」**

Xcode 27 beta を使っている場合、XcodeBuildMCP を v2.7.0 にアップデートするだけで Issue #80425 のシミュレーター検出エラーが解消されます。以下の手順で更新と確認を行ってください。

**1. アップデートを適用する**

Claude Code の `.claude/settings.json` または `~/.claude/settings.json` で XcodeBuildMCP を npx 経由で参照している場合、バージョンを明示的に指定して更新します。

```json
{
  "mcpServers": {
    "XcodeBuildMCP": {
      "command": "npx",
      "args": [
        "-y",
        "xcodebuildmcp@2.7.0"
      ]
    }
  }
}
```

グローバルにインストールしている場合は：

```bash
npm update -g xcodebuildmcp
# または特定バージョンを固定する場合
npm install -g xcodebuildmcp@2.7.0
```

**2. スキーマ v3 の変更点を確認する**

v2.7.0 からテスト出力に `testCases` が追加されました。Claude Code に「テスト結果をまとめて」と依頼したときのレスポンスが以下のように変わります。

```
# v2 形式（旧）
{ "schemaVersion": 2, "passed": 42, "failed": 1, ... }

# v3 形式（新）
{
  "schemaVersion": 3,
  "passed": 42,
  "failed": 1,
  "testCases": [
    { "name": "testLogin", "duration": 0.412, "status": "passed" },
    { "name": "testCheckout", "duration": 12.803, "status": "failed" }
  ]
}
```

CLAUDE.md に「テスト結果は `testCases` のうち `status: failed` のものを優先的に報告する」と記述しておくと、失敗テストへの対処が速くなります。

**3. ビルド構成の破壊的変更を確認する（CI 要注意）**

`--configuration` を省略した場合の挙動が変わっています。これまで常に Debug ビルドになっていた箇所が、スキームのデフォルト設定（Release 等）を使うようになる場合があります。CI の `xcodebuild` コマンドに `-configuration Debug` を明示的に指定していない場合は確認してください。

```bash
# 明示的に指定することで v2.6.x と同じ挙動を維持できる
xcodebuild -scheme MyApp -configuration Debug -destination 'generic/platform=iOS' build
```
