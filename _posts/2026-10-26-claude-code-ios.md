---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-10-26"
date: 2026-10-26 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [v2.1.304（2026-10-19）— macOS サンドボックスで export した環境変数が Bash に伝わらない問題を修正](https://code.claude.com/docs/en/changelog) — `~/.zshrc` 等で export した環境変数が、Claude Desktop の Code タブ内のサンドボックス Bash に見えない問題を修正。`xcodebuild` や `fastlane` に渡す `DEVELOPER_DIR`・`FASTLANE_APPLE_APPLICATION_SPECIFIC_PASSWORD` 等の環境変数が Claude Code セッション内で正しく読み込まれるようになる。iOS CI を Claude Desktop 経由で動かしているチームには直接効く修正。

## 🛠 GitHub の動き

- [getsentry/MobileBuildMCP v2.7.1 — XcodeBuildMCP から MobileBuildMCP にリネーム（breaking change）](https://github.com/getsentry/MobileBuildMCP/releases/tag/v2.7.1) — npm パッケージ名・CLI バイナリ名（`mobilebuildmcp`・`mobilebuildmcp-doctor`）・Homebrew formula・環境変数プレフィックス（`MOBILEBUILDMCP_*`）・リソース URI スキーム（`mobilebuildmcp://`）・設定ディレクトリ・ステートディレクトリすべてが新名称に移行。旧 `xcodebuildmcp.com` ドメインは廃止。既存ユーザーは設定の全面更新が必要（フォールバックなし）。v2.7.1 は 2026-09-23 リリース済みだが、未移行のチームは早急に対応を。

## 📝 日本語コミュニティ

- 該当なし（2026-10-26 時点で Zenn・Qiita に Claude Code × iOS / Swift / Xcode 関連の新規記事は確認できず）

## 🌐 海外コミュニティ / Tips

- [How to Run Xcode 27 MCP Server Headless with Claude Code（mehmetbaykar.com）](https://mehmetbaykar.com/posts/how-to-run-xcode-27-mcp-server-headless-with-claude-code/) — Xcode 27 の `xcrun mcpbridge` を GUI なし環境（CI・リモートセッション）で起動する手順をまとめた解説。Xcode 27 の Intelligence 設定で「Allow external agents to use Xcode tools」を有効化してから mcpbridge を起動すると、Claude Code が診断情報・シンボル解決・SwiftUI Preview レンダリング結果を取得できる。ヘッドレスモードではリポジトリフォルダを明示的に指定しアクセス範囲を絞ることを推奨。
- [Two MCP Servers Made Claude Code an iOS Build System（blakecrosley.com）](https://blakecrosley.com/blog/xcode-mcp-claude-code) — MobileBuildMCP（旧 XcodeBuildMCP）と Xcode 27 mcpbridge を併用することで、Claude Code が iOS ビルド・テスト・シミュレーター操作・UI 診断の全工程を担えるようになった実践報告。CI 環境では Xcode 27 mcpbridge が使えないため MobileBuildMCP を継続使用し、ローカルではmcpbridge でビジュアルフィードバックを補完するという 2 サーバー構成が紹介されている。

## 💡 今日のおすすめ実践 Tip

**XcodeBuildMCP → MobileBuildMCP v2.7.1 への移行チェックリスト**

v2.7.1 でリネームが確定した。既存の Claude Code 設定が旧名称のままだと MCP サーバーが認識されなくなるため、以下の手順で一括更新すること。

**1. npm / npx 経由の場合**

```bash
# 旧パッケージをアンインストール
npm uninstall -g xcodebuildmcp

# 新パッケージをインストール
npm install -g mobilebuildmcp
```

`claude mcp` の登録コマンドも更新:

```bash
# 旧設定を削除
claude mcp remove XcodeBuildMCP

# 新設定を追加
claude mcp add MobileBuildMCP -- npx -y mobilebuildmcp@latest mcp
```

**2. Homebrew の場合**

```bash
brew uninstall xcodebuildmcp
brew tap getsentry/mobilebuildmcp
brew install mobilebuildmcp
```

**3. 環境変数を使っている場合**

`XCODEBUILDMCP_*` で始まる環境変数はすべて `MOBILEBUILDMCP_*` に更新。

**4. 動作確認**

```bash
mobilebuildmcp-doctor
```

旧 `xcodebuildmcp-doctor` が入っていた場合は上記に差し替え。設定が正しければ接続先 Xcode とツール一覧が表示される。
