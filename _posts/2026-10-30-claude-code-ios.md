---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-10-30"
date: 2026-10-30 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- 2026-10-30 時点の最新版は v2.1.312（2026-10-27）のまま。新規リリースなし。v2.1.312 の内容（CLAUDE.md 大ファイル上書きバグ修正・`oidc.user_id_claim` 追加・5 時間使用ウィンドウの名称変更など）は 10/28 キャッチアップを参照。

## 🛠 GitHub の動き

- [#105524（2026-10-27 open）Desktop app Code タブ: MCP ツール呼び出しが per-server `timeout` を無視して 60 秒でカット、Xcode 27.1 ではビルドまでキャンセルされる](https://github.com/anthropics/claude-code/issues/105524) — Claude Desktop の Code タブで実行する MCP ツール呼び出しが、`"timeout": 1800000` などのサーバー側設定や `MCP_TOOL_TIMEOUT` 環境変数を無視して 61 秒でタイムアウトする問題。Xcode 26 時代はタイムアウトしてもXcodeのビルドは継続していたが、Xcode 27.1 の `xcrun mcpbridge` が MCP キャンセル通知を正しく処理するようになったため、60 秒経過で **Xcode のビルドやテストも実際にキャンセル**される。`BuildProject`・`RunAllTests`・`RunSomeTests` が事実上使用不可。暫定回避策はターミナルの `claude` CLI を使う（CLI では per-server `timeout` が正常に機能することを報告者が確認済み）か、Xcode で手動ビルドして `.xcresult` をClaudeに読ませる方法。

- [#88217（2026-08-20 open）iOS Simulator MCP/パネル: attach が誤ったデバイスを報告、タップが別シミュレーターに届く・シャットダウン済み端末が attached 扱い](https://github.com/anthropics/claude-code/issues/88217) — 依然 open のまま。複数シミュレーターを起動した状態で、Claude Code が「attached」と報告するデバイスと実際にアクションが届くデバイスがズレる問題。削除済み・shutdown 済み端末が attached 状態のまま残るケースも含む。Xcode 27 環境で複数端末を並列使用するチームは引き続き注意。

## 📝 日本語コミュニティ

- [Xcode MCP × Claude Code プラグインで iOS ビルドを自動化する（Zenn / kyoichi）](https://zenn.dev/kyoichi/articles/claude-code-plugin-xcode-mcp-hybrid) — Xcode 26.3 の `xcrun mcpbridge`（公式 MCP）と MobileBuildMCP を Claude Code のプラグイン機構で連携し、iOS ビルド・テスト・シミュレーター操作を自動化する構成を解説。公式 MCP の SwiftUI Preview / Swift REPL を活かしつつ、ビルド・テスト部分を MobileBuildMCP に委ねる 2 サーバー構成が詳述されている。

- [Xcode 26.3 の MCP とは何か — なぜ必要で、どう使うのか。7 つの Skills で実践する（Zenn / dsgarage）](https://zenn.dev/dsgarage/articles/xcode-263-mcp-skills) — Xcode 26.3 の `xcrun mcpbridge` が持つ RenderPreview・ExecuteSnippet・Symbol Lookup などのスキル群の全体像と、Claude Code からの呼び出し方をまとめた解説。UIKit → SwiftUI 一括マイグレーションや Apple ドキュメント検索の実践例も紹介されている。

## 🌐 海外コミュニティ / Tips

- [mcs-cli/ios — iOS 開発向け Claude Code tech pack（GitHub / mcs-cli）](https://github.com/mcs-cli/ios) — MobileBuildMCP・Sosumi（Apple ドキュメント検索 MCP）・起動中シミュレーターの名前と UUID を Claude Code セッション開始時に自動通知する仕組み・`.xcodeproj`/`.xcworkspace` の自動検出・`xcrun` や `simctl` の直接呼び出し禁止ルールをひとまとめにした tech pack。`mcs` コマンドでプロジェクトごとにインストールでき、macOS + Xcode CLT + Claude Code が前提。

## 💡 今日のおすすめ実践 Tip

**Desktop app Code タブでの Xcode 27 ビルドキャンセル問題（#105524）の 3 つの回避策**

#105524 の通り、Claude Desktop の Code タブでは MCP ツール呼び出しが 60 秒でカットされる。Xcode 27.1 の `mcpbridge` がキャンセル通知に応答するようになったため、大規模アプリの `BuildProject` や全テスト実行は常に途中でキャンセルされてしまう。修正版のリリースまでに取れる暫定策を示す。

**回避策 1: ターミナルの `claude` CLI を使う（最も確実）**

CLI 経由では per-server `timeout` 設定が正しく機能することが報告者によって確認済み。

```bash
# ~/.claude.json の mcpServers に xcrun mcpbridge を登録済みの場合
claude  # ターミナルで起動して Code タブではなく CLI セッションで作業
```

**回避策 2: Xcode でビルドし `.xcresult` を読ませる**

```
1. Xcode で ⌘B または ⌘U を手動実行
2. Claude Code に「最新の .xcresult からビルド/テスト結果を確認して」と依頼
```

MobileBuildMCP の `get_build_results` ツールで `.xcresult` を取得させる方法も有効。

**回避策 3: 60 秒以内で終わる小粒テストのみ MCP 経由で実行**

パッケージスキームの軽量ユニットテストに限定して MCP 経由で走らせ、アプリ全体のビルドや統合テストは回避策 1/2 で補う運用が現実的。

Issue #105524 は 2026-10-27 に open されており、まだ対応版はリリースされていない。changelog を継続ウォッチすること。
