---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-10-01"
date: 2026-10-01 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- **[v2.1.286（2026-09-30）リリース](https://github.com/anthropics/claude-code/releases/tag/v2.1.286)** — iOS 開発環境で特に影響が大きい修正が複数含まれる。**`claude --resume` / `--continue` が並列ツール呼び出しを含むクラッシュ済みセッションで正常にターンを復元できなかったバグを修正**（長時間の Xcode ビルドや並列テスト実行中のクラッシュ後に便利）。**Remote Control セッションが組織ポリシー変更後もずっと接続したままになる問題を修正**。**大きな履歴を持つクラウドセッションがウェイクアップしなくなる問題を修正**。複数のパーミッション要求がスタックしたときに「2 of 5」のようなカウンターが表示されるように（確認すべき権限リクエスト数が一目でわかる）。VS Code 拡張: レスポンスをその場で保存して画面外にスクロールしても参照し続けられる **ブックマーク機能を追加**。

- **iOS Simulator 互換性バグ 2 件が v2.1.285/286 対応ウィンドウ内でクローズ**: [Issue #95085（Xcode 27 で Simulator.app が存在せずタイムアウト）](https://github.com/anthropics/claude-code/issues/95085) および [Issue #92442（macOS 27 beta で Metal/Core Image init クラッシュ）](https://github.com/anthropics/claude-code/issues/92442) がいずれも 2026-09-29 にクローズ。Xcode 27 の DeviceHub.app 移行に伴う Claude ツール側の対応が進んでいる。

## 🛠 GitHub の動き

- **[Issue #97826（2026-09-28 作成 / OPEN）— iOS Simulator ツールが DeviceHub.app 上のブート済みデバイスを検出できない](https://github.com/anthropics/claude-code/issues/97826)** — Xcode 27.0 で `Simulator.app` が `/Applications/Xcode.app/Contents/Applications/DeviceHub.app`（Bundle ID: `com.apple.dt.Devices`）に置き換えられたことで、Claude の iOS Simulator MCP ツールが旧パスを探して「No booted simulator named ''」と報告するリグレッション。`xcrun simctl` は正常に動作するため、暫定回避策として `simctl` 経由のコマンド（`xcrun simctl list devices booted`、`xcrun simctl io <udid> screenshot` など）を使用する方法がある。

- **[Issue #95466（2026-09-18 作成 / OPEN）— Xcode 27 アップグレード後にタッチ/タップ注入がサイレントに無効化される](https://github.com/anthropics/claude-code/issues/95466)** — Xcode 27 で Simulator.app がヘッドレス DeviceHub に置き換えられた影響で、Claude Code の UI 自動化ツールで `touch` / `tap` を実行しても無応答になる。エラーは出ずに静かにスキップされるため気づきにくい。Xcode 27 上で UI テスト自動化を組み込んでいる開発者は注意。

## 📝 日本語コミュニティ

- 該当なし（2026-10-01 時点で Zenn・Qiita の新規 Claude Code × iOS / Swift / Xcode 記事は確認できず）

## 🌐 海外コミュニティ / Tips

- **[MobileBuildMCP v2.7.1 — XcodeBuildMCP からリネーム完了（2026-09 後半）](https://github.com/fastmcp-me/xcodebuildmcp)** — 2026 年 9 月後半リリースの v2.7.1 でプロジェクトが正式に `XcodeBuildMCP` から `MobileBuildMCP` に改名。npm パッケージ・CLI バイナリ・環境変数・設定ディレクトリ名がすべて変更されており、**設定ディレクトリが `.xcodebuildmcp/` から `.mobilebuildmcp/config.yaml` に変更（旧ディレクトリへのフォールバックなし）**。既存ユーザーは旧 `.xcodebuildmcp/` を削除し `.mobilebuildmcp/config.yaml` に移行が必要。`claude mcp add MobileBuildMCP -- npx -y mobilebuildmcp@latest mcp` で再登録する。

## 💡 今日のおすすめ実践 Tip

**「Xcode 27 移行期の iOS Simulator 代替コマンド集（Issue #97826 / #95466 暫定対策）」**

Claude Code の iOS Simulator ツールが Xcode 27 の DeviceHub.app に対応完了するまでの間、`xcrun simctl` を直接使う Bash ツール呼び出しで代替できます。

```bash
# ブート済みデバイス一覧の取得（simctl は DeviceHub でも正常動作）
xcrun simctl list devices booted

# スクリーンショット取得
xcrun simctl io booted screenshot /tmp/screenshot.png

# アプリ起動
xcrun simctl launch booted com.example.MyApp

# タップ（UI Automation の代替）
xcrun simctl ui booted click 195 422
```

CLAUDE.md にこれらをコマンドリファレンスとして記載しておくと、Claude が `iOS Simulator MCP ツール` が使えない状況でも自動的に `Bash` ツール経由で `simctl` を呼び出してくれるようになります。

```markdown
## iOS Simulator 操作（Xcode 27 以降）
Claude Code の iOS Simulator ツールが DeviceHub を認識しない場合は
xcrun simctl コマンドを代わりに使ってください（bash ツール経由で実行可能）。
```
