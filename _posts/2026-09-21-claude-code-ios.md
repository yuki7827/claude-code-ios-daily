---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-09-21"
date: 2026-09-21 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- 該当なし（2026-09-21 時点の最新版は引き続き v2.1.278。9/19 リリース以降、新しい Claude Code リリースは確認されていない）

## 🛠 GitHub の動き

- **[Issue #95085（2026-09-17 作成 / OPEN）— iOS Simulator コントロールツール: Xcode 27 で attach/boot が常にタイムアウト（`Simulator.app` 廃止・`DeviceHub.app` への置き換えが原因）](https://github.com/anthropics/claude-code/issues/95085)** — `xcrun simctl bootstatus -b` でデバイスが `Booted` 状態と確認できるにもかかわらず、`control(attach)` / `launch` が毎回「Simulator did not finish booting within 120s」でタイムアウトする。根本原因: iOS Simulator ツールのアタッチ/ブートパスが bundle ID `com.apple.iphonesimulator`（`Simulator.app`）を探すが、**Xcode 27 ではこの app が完全に廃止されており** `lsregister -dump | grep com.apple.iphonesimulator` が何も返さない。代わりに `com.apple.dt.Devices`（`DeviceHub.app`）が配置されているが、IPC インターフェースが旧 Simulator.app と異なるため、ツールが DeviceHub に対してアタッチプロトコルを成立させられない。これまで既報の「tap/swipe が silent no-op になる」問題（#95466）や「sandbox による IO ポート失敗」（#95191）とは独立した障害で、**パネルを開く前の段階から詰まる**。提案された修正: Xcode 27 以降を検出した場合は `com.apple.dt.Devices` をターゲットにするフォールバックロジックを追加する。**暫定回避策**: `xcrun simctl launch` / `xcrun simctl openurl` でアプリを直接起動し、スクリーンショットは `xcrun simctl io <UDID> screenshot` で取得する。

## 📝 日本語コミュニティ

- 該当なし（2026-09-21 時点で新規の Zenn・Qiita 記事は確認できず）

## 🌐 海外コミュニティ / Tips

- **[XcodeBuildMCP v2.7.0（getsentry、2026-07-23）— Xcode 27 Device Hub 経由の UI オートメーション対応](https://github.com/getsentry/XcodeBuildMCP/releases/tag/v2.7.0)** — 今週取り上げている Xcode 27 の iOS Simulator tap/swipe 障害（#95466・#95191・#95085）に対する現時点で最も実用的な回避策として、XcodeBuildMCP v2.7.0 が Xcode 27 の DeviceHub 経由の UI 自動化をネイティブサポートした（7月リリース）。`xcodebuildmcp control tap` / `swipe` / `keyboard` が DeviceHub.app（`com.apple.dt.Devices`）と直接通信するよう実装が更新されており、旧 `Simulator.app` アーキテクチャへの依存が解消されている。Claude 組み込みの iOS Simulator パネル（`claude-ios-sim`）が sandbox や DeviceHub 未対応で詰まっている環境でも、XcodeBuildMCP 経由なら tap/swipe を実行できる。主な破壊的変更: build/test の構造化結果が `schemaVersion: "3"` に更新（バリデーターの更新が必要）、未指定の build configuration がスキームの設定に従うようになった（以前は Debug がデフォルト）。

## 💡 今日のおすすめ実践 Tip

**「XcodeBuildMCP v2.7.0 を Xcode 27 環境に導入して tap/swipe 問題を根本解決する」**

今週の記事で取り上げてきた Xcode 27 の iOS Simulator 障害（#95085・#95191・#95466）はいずれも Claude 組み込みの `claude-ios-sim` ヘルパーの問題です。Anthropic 側の修正を待たずに開発ループを回すには、XcodeBuildMCP v2.7.0 の DeviceHub 対応を活用するのが最速です。

**ステップ 1: XcodeBuildMCP v2.7.0 以上をインストール**

```bash
# Homebrew 経由
brew install getsentry/tap/xcodebuildmcp

# npm 経由
npm install -g @getsentry/xcodebuildmcp

# バージョン確認（2.7.0 以上であることを確認）
xcodebuildmcp --version
```

**ステップ 2: Claude Code に MCP として登録**

```bash
claude mcp add XcodeBuildMCP -- xcodebuildmcp
```

または `.mcp.json` に直接記述:

```json
{
  "mcpServers": {
    "XcodeBuildMCP": {
      "command": "xcodebuildmcp",
      "args": []
    }
  }
}
```

**ステップ 3: CLAUDE.md に制約と使い方の指針を記述**

```markdown
## iOS Simulator ツールの切り替え（Xcode 27 環境 / 2026-09 時点）

Claude 組み込みの iOS Simulator パネル（mcp__Claude_Code_iOS_Simulator__control）は
Xcode 27 環境で attach/tap/swipe が動作しない（Issue #95085・#95191・#95466）。

代替: XcodeBuildMCP (v2.7.0+) を使う
- ビルド:  xcodebuildmcp:build
- 起動:    xcodebuildmcp:run  または xcrun simctl launch booted <bundle-id>
- tap:     xcodebuildmcp:control {"action":"tap","x":...,"y":...}
- swipe:   xcodebuildmcp:control {"action":"swipe",...}
- SS取得:  xcodebuildmcp:screenshot  または xcrun simctl io <UDID> screenshot /tmp/sim.png
```

**ステップ 4: Claude への典型的な指示**

```
XcodeBuildMCP を使って MyApp スキームをビルドし、
iPhone 17 Pro シミュレーターで起動してください。
ログイン画面のスクリーンショットを撮り、
メールフィールドをタップして test@example.com を入力し、
パスワードフィールドに入力後、サインインボタンをタップしてください。
各ステップ後にスクリーンショットで状態を確認してください。
```

**注意点**

XcodeBuildMCP v2.7.0 の破壊的変更に注意: 以前 `Debug` ビルドをデフォルトで使用していたスクリプトは、`--configuration Debug` を明示しないと意図しないリリースビルド設定になる可能性があります。また、CI スクリプトが build/test の構造化出力を parse している場合は `schemaVersion: "3"` への対応が必要です。
