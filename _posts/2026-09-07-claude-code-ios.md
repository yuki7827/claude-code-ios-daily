---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-09-07"
date: 2026-09-07 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- **v2.1.263（2026-09-06）リリース**（[Claude Code 変更ログ](https://code.claude.com/docs/en/changelog)）— CLI のバグ修正と信頼性改善。クラッシュ削減とコマンド安定性の向上を目的としたパッチリリース。詳細な変更点は [X（旧 Twitter）の @ClaudeCodeLog](https://x.com/ClaudeCodeLog/status/2096435610525315378) で確認できる。iOS 開発で長時間の xcodebuild コマンドを Claude Code に任せている場合、CLI の安定性向上はビルド失敗率の低減に直結するため、最新版へのアップデート（`npm install -g @anthropic-ai/claude-code`）を推奨。

## 🛠 GitHub の動き

- **[ndrblinov/xcode-mcp-manager（新規公開 / v0.0.1）](https://github.com/ndrblinov/xcode-mcp-manager)** — Apple の `xcrun mcpbridge`（Xcode 26.3 以降の公式 MCP ブリッジ）を自動起動・再接続・パーミッションダイアログ自動却下するスーパーバイザー MCP サーバー。これまでは「Claude Code を起動する前に Xcode を必ず起動しておく」「プロジェクト切替のたびに手動でパーミッションダイアログを承認する」という手間があったが、このツールが 2 秒ごとに Xcode プロセスの存在を監視し、起動を検知したら mcpbridge を自動スポーン・AppleScript でダイアログを自動却下してくれる。Xcode 26.3 以上・Node.js 22 以上が必要。エージェントの無人実行（GitHub Actions での自動レビュー等）を Xcode MCP 経由で行う際の課題を解決する実験的プロジェクトとして注目される。

- **[lapfelix/XcodeMCP — JXA で Xcode を直接コントロールする MCP サーバー](https://github.com/lapfelix/XcodeMCP)** — `xcodebuild` CLI ではなく JavaScript for Automation（JXA）で Xcode アプリ本体を操作する MCP サーバー。XcodeBuildMCP が `xcodebuild` コマンドを叩くのに対し、こちらは Xcode の GUI を JXA 経由で直接制御するアプローチ。XCLogParser を使った詳細なビルドエラー解析・テスト結果の UI 階層抽出・Apple 公式 Xcode MCP サーバーと並列で動かせる「サイドキックモード」が特徴。XcodeBuildMCP との使い分けの観点では、CI 環境など Xcode GUI が不要な場合は XcodeBuildMCP、Xcode 自体を AI に操作させたい場合や XCResult の詳細解析が必要な場合は XcodeMCP が向いている。

## 📝 日本語コミュニティ

- [Claude Code の新機能 iOS シミュレータ連携機能はこう使う（Zenn / tabetaaaaaaa）](https://zenn.dev/tabetaaaaaaa/articles/75d748f4835354) — Claude Code Desktop の iOS Simulator ペイン（Week 30 追加機能）の実践的な使い方を解説した記事。

- [Claude Code と XcodeBuildMCP で簡単な iOS アプリを作ってみました（Zenn / kobashuu）](https://zenn.dev/kobashuu/articles/42a551b609c06d) — XcodeBuildMCP を使って Claude Code から iOS アプリをゼロからビルドする手順をまとめた記事。

## 🌐 海外コミュニティ / Tips

- **[jamesrochabrun/ClaudeCodeUI — SwiftUI で Claude Code UI を macOS アプリに埋め込む Swift パッケージ](https://github.com/jamesrochabrun/ClaudeCodeUI)** — `ClaudeCodeSDK`（Claude Code を subprocess として呼び出す SDK）の UI レイヤーにあたる Swift Package ライブラリ。メッセージ履歴・ファイル添付（画像・PDF）・コードスニペット表示・セッション管理・MCP サポートを備えた macOS ネイティブの会話 UI を、自作 macOS アプリに数行の SwiftUI コードで統合できる。iOS アプリへの組み込みは macOS の `Process` API に依存するため非対応だが、Mac Catalyst や macOS 向けの社内ツールに Claude Code の対話 UI を組み込む用途では有力な選択肢。macOS 14.0 以上・Xcode 15.0 以上・Swift 5.9 以上が必要。

- 該当なし（dev.to 等の英語記事で iOS 文脈の新規記事は確認できず）

## 💡 今日のおすすめ実践 Tip

**「XcodeMCP と XcodeBuildMCP の使い分けガイド」**

iOS 開発で Xcode を Claude Code に連携させる MCP サーバーは複数存在します。今日取り上げた `lapfelix/XcodeMCP` と定番の `getsentry/XcodeBuildMCP` は、アプローチが根本的に異なります。

| 観点 | XcodeBuildMCP（getsentry） | XcodeMCP（lapfelix） |
|------|--------------------------|---------------------|
| **制御方式** | `xcodebuild` CLI 経由 | JXA（JavaScript for Automation）でXcode GUI を直接操作 |
| **CI 環境** | ヘッドレス対応（GUI 不要） | Xcode 起動が必要 |
| **テスト結果** | xcodebuild 標準出力を解析 | XCLogParser で XCResult を詳細解析 |
| **UI テスト** | `ui-automation/*` ツール群で対応 | UI 階層抽出に強み |
| **Apple 公式 MCP との共存** | 代替として使う | サイドキックモードで並列運用可 |

**どちらを選ぶか**

```
CI / GitHub Actions でヘッドレスビルドを回したい
  → XcodeBuildMCP（GUI 不要、並列 DerivedData 対応）

Xcode 26.3 のネイティブ MCP（xcrun mcpbridge）に追加で
XCResult の詳細解析やビルドエラーの精密診断をしたい
  → XcodeMCP（Apple 公式 MCP との並列運用 = サイドキックモード）

Xcode 起動を自動検知して mcpbridge を自動接続したい
  → xcode-mcp-manager を上流に挟み、その下で XcodeMCP or Apple 公式を使う
```

**`xcode-mcp-manager` を Claude Code の設定に追加する例**

```json
// .claude/settings.json
{
  "mcpServers": {
    "xcode-mcp-manager": {
      "type": "stdio",
      "command": "npx",
      "args": ["xcode-mcp-manager"]
    }
  }
}
```

これを設定しておくと、Claude Code を起動した後から Xcode を開いても自動的に mcpbridge が接続され、Xcode の先起動を忘れてセッションをやり直すストレスがなくなります。
