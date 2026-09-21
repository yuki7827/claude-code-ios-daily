---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-09-22"
date: 2026-09-22 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- 該当なし（2026-09-22 時点の最新版は引き続き v2.1.278。9/19 リリース以降、新しい Claude Code リリースは確認されていない）

## 🛠 GitHub の動き

- **[Issue #85132（2026-08-08 作成 / OPEN）— iOS Simulator MCP `text` アクションが US キーボードレイアウトを前提としているため、Nordic 等の非 US 配列で `@` → `"` などにミスマップされる](https://github.com/anthropics/claude-code/issues/85132)** — macOS の System Keyboard Layout（`TISCopyCurrentKeyboardLayoutInputSource`）を参照してキーイベントを生成するのではなく、ハードコードされた US-ASCII テーブルで文字→仮想キーコードのマッピングを行っているのが原因。スウェーデン語・フィンランド語・デンマーク語・ドイツ語・フランス語配列など、英語以外のキーボードを使用している iOS 開発者に広く影響する（`@` や `{` / `}` / `[` / `]` 等の記号が化ける）。2026-09-20 に更新があり現在 OPEN。提案された修正: `UCKeyTranslate` API で現在のキーボードレイアウトから逆引きした仮想キーコードを使うよう変更する。**暫定回避策**: `text` アクションの代わりに `xcrun simctl io <UDID> keyboard type "<text>"` を使うか、入力前に一時的に US キーボードレイアウトに切り替える。

## 📝 日本語コミュニティ

- 該当なし（2026-09-22 時点で新規の Zenn・Qiita 記事は確認できず）

## 🌐 海外コミュニティ / Tips

- **[mcs-cli/ios — XcodeBuildMCP + Sosumi MCP をバンドルした Claude Code 向け iOS 開発 MCP パック](https://github.com/mcs-cli/ios)** — `mcs pack add mcs-cli/ios` + `mcs sync` の 2 コマンドで XcodeBuildMCP（ビルド・テスト・シミュレーター操作）と Sosumi MCP（Apple 公式ドキュメントのインライン検索）の依存関係インストールおよびシミュレーターステータス検出フックの設定まで自動化される iOS 開発環境構築パック。主な特徴: ① シミュレーターの UDID 自動検出と起動状態レポート、② Apple ドキュメント検索（ローカルインデックス不要）、③ `xcrun`/`xcodebuild` の直接呼び出しを制限してすべてのビルド操作を XcodeBuildMCP 経由に強制するルール、④ Swift 編集後の自動フォーマット・リント。`mcs >= 2026.3.6` が必要。Xcode 27 環境での XcodeBuildMCP v2.7.0（DeviceHub 対応）と組み合わせることで、先週取り上げた Simulator tap/swipe 障害の回避策もパックの恩恵として受けられる。

## 💡 今日のおすすめ実践 Tip

**「mcs-cli/ios パックで iOS 開発 MCP 環境を 2 コマンドで整える」**

先週の記事で紹介した XcodeBuildMCP の手動セットアップ（`claude mcp add` + `.mcp.json` 編集）は 5〜10 ステップかかりましたが、`mcs-cli/ios` パックを使えば XcodeBuildMCP・Sosumi MCP・シミュレーターフックまでをまとめて 2 コマンドで導入できます。

**インストール（前提: mcs >= 2026.3.6）**

```bash
# mcs 自体のインストール（未導入の場合）
brew install mcs-cli/tap/mcs

# iOS 開発パックを追加
mcs pack add mcs-cli/ios

# プロジェクトルートで同期（.mcp.json + CLAUDE.md フラグメントを生成）
cd /path/to/MyApp
mcs sync

# ヘルスチェック
mcs doctor
```

**`mcs sync` で生成される主な設定**

```json
// .mcp.json（自動生成）
{
  "mcpServers": {
    "XcodeBuildMCP": {
      "command": "xcodebuildmcp",
      "args": []
    },
    "sosumi": {
      "command": "sosumi-mcp",
      "args": []
    }
  }
}
```

```markdown
<!-- CLAUDE.md に自動追記されるフラグメント -->
## iOS Build Rules
- Never call xcrun or xcodebuild directly — route all build/test/sim actions through XcodeBuildMCP tools.
- Use XcodeBuildMCP:build, XcodeBuildMCP:test, XcodeBuildMCP:run for all build operations.
- For Apple API questions, use sosumi:search before writing code.
- Simulator UDID (auto-detected): see .mcs/sim-status.json
```

**Xcode 27 環境での追加メリット**

`mcs-cli/ios` が依存する XcodeBuildMCP v2.7.0 には Xcode 27 DeviceHub 経由の UI 自動化対応が含まれているため（先週の #95085 / #95466 対応記事参照）、パックを入れるだけで tap/swipe の暫定回避策も同時に適用されます。

新規 iOS プロジェクトを始める際の「最初の 10 分セットアップ」として組み込む価値があります。
