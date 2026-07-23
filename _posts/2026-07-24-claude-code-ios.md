---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-07-24"
date: 2026-07-24 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Claude Code v2.1.218（2026-07-22）](https://code.claude.com/docs/en/changelog) — iOS 開発ワークフローに関係する変更が 2 点。**① `/code-review` がバックグラウンドサブエージェントで実行されるよう変更**：レビュー作業が会話コンテキストを消費しなくなり、`/code-review` を実行しながら Xcode ビルドの指示を続けることができる。スタックしたスラッシュコマンドを対象として引き継ぐ動作も維持。**② auto mode の自動承認ルールを拡充**：`dangerous-rm`・`background-&`・`suspicious-Windows-path` の 3 チェックがダイアログなしで自動処理されるよう変更。fastlane や `xcodebuild` をバックグラウンド(`&`)で走らせる際に確認ダイアログが出なくなるケースが増える。その他 `/ultrareview` の引数エラー修正・VoiceOver 読み上げ修正・深いディレクトリツリーでのクラッシュ修正も含む。

- [Claude Code v2.1.217（2026-07-21）](https://code.claude.com/docs/en/changelog) — **同時実行サブエージェント上限を設定可能に**（デフォルト 20、`CLAUDE_CODE_MAX_CONCURRENT_SUBAGENTS` 環境変数で変更可）。複数ターゲット（iPhone / iPad / Vision Pro）を並列ビルドする際のリソース制御に直接使える。あわせてバックグラウンドセッション内のシンボリックリンクディレクトリ正規化バグを修正（XcodeBuildMCP を ワークツリー分離と組み合わせている場合に影響）。MCP ツール出力が切り詰められた際に全体を保持し続けるメモリリークも修正。

## 🛠 GitHub の動き

- [Issue #80472 — macOS 27 beta 4 で iOS シミュレーターヘルパーが Metal/CoreImage seatbelt でクラッシュ（SIGABRT）— 回避策あり（OPEN / 2026-07-23）](https://github.com/anthropics/claude-code/issues/80472) — `claude-ios-sim` ヘルパーがシミュレーターパネルのビデオストリームを開始した瞬間に SIGABRT でクラッシュし、パネルが起動しない問題。根本原因は macOS 27 beta 4 で Metal のシェーダーキャッシュがバンドル ID ごとのディレクトリ（`$(getconf DARWIN_USER_CACHE_DIR)/com.anthropic.claude.ios-sim/`）に変更されたが、ヘルパーの seatbelt プロファイルがこのディレクトリへの書き込みを禁止しているため、`MTLGetShaderCachePath()` が nil を返し `CIContext` 初期化でクラッシュする。**回避策（以下を実行するとパネルが正常動作）**：`C_DIR=$(getconf DARWIN_USER_CACHE_DIR) && mkdir -p "$C_DIR/com.anthropic.claude.ios-sim/com.apple.metal/archiveUsage.db" "$C_DIR/com.anthropic.claude.ios-sim/com.apple.metalfe" "$C_DIR/com.anthropic.claude.ios-sim/com.apple.gpuarchiver"`。なお `/var/folders` がクリアされると再発するため、クリーンアップ後は再実行が必要。修正は seatbelt プロファイルへの `file-write*` 許可追加が本筋で Anthropic 対応待ち。macOS 26.x では不再現。

- [Issue #80425 — Xcode beta（DeviceHub.app）でシミュレーターパネルの `attach` が「No booted simulator found」を返す（OPEN / 2026-07-23）](https://github.com/anthropics/claude-code/issues/80425) — Xcode 27 beta で Apple がシミュレーターの GUI プロセスを `Simulator.app` から `DeviceHub.app` に変更した影響で、シミュレーターパネルの起動済み端末検出が常に失敗する。`xcrun simctl list devices booted` では正常に Booted と返るにもかかわらず `attach` がエラーを返す。パネル側の検出ロジックが `Simulator.app` プロセス / バンドル ID をハードコード参照していることが原因。Xcode 27 beta + DeviceHub 環境では現時点でシミュレーターパネルの使用は不可能（XcodeBuildMCP で代替可能）。

- [Issue #80413 — シミュレーターパネルに フォーカス中に Cmd+Q で Claude Desktop 全体が終了する（OPEN / 2026-07-23）](https://github.com/anthropics/claude-code/issues/80413) — 組み込みシミュレーターパネルは独立プロセスではなく Claude Desktop の Electron ウィンドウ内に描画されるため、パネルにフォーカスがある状態で Cmd+Q を押すとアプリ全体が終了しセッションが失われる。Apple の `Simulator.app` の Cmd+Q 終了操作の筋肉記憶で踏むため注意が必要。同 Issue では横向き固定アプリをパネルが縦向きのまま表示する問題（黒帯表示）も報告されている。当面の回避策：シミュレーターパネルを操作する際は Cmd+Q を使わず、Claude Desktop 側のウィンドウ枠をクリックしてフォーカスを移してから終了する。

## 📝 日本語コミュニティ

- [iOSアプリ開発未経験者がClaudeCodeを使って1週間でリリースしたお話（Qiita / chatrate）](https://qiita.com/chatrate/items/bd2703d68a2bb8fffe08) — Swift / iOS 開発経験なしの著者が Claude Code だけを頼りにゼロからアプリを App Store にリリースするまでの 1 週間を記録した記事。「機能実装の指示よりも UI/UX の指示に時間を費やした」という体験談が中心で、コードを書かず Claude Code への指示出しに集中することで開発を完結させた過程を紹介。XcodeBuildMCP や直接 `xcodebuild` 呼び出しなど、具体的なビルドフローも言及あり。Claude Code を iOS 開発の入口として使うケースの実例として参考になる。

- [Claude / Anthropic 関連ニュースまとめ（2026-07-23）（Qiita / homhom44）](https://qiita.com/homhom44/items/9ebe7abd9aec61dfc2bf) — 2026-07-23 分の Claude / Anthropic 関連ニュースをコンパクトにまとめた記事。iOS シミュレーターパネル関連 Issue（#80288 / #80295 等）と v2.1.218 の変更点がカバーされており、週次でまとめを追いたい場合に便利。

## 🌐 海外コミュニティ / Tips

- [Xcode 26.3 Makes Your IDE an MCP Server — Any AI Agent Can Now Build iOS Apps（Jason Moon / jasonmoon.dev / 2026-03-05）](https://jasonmoon.dev/blog/2026-03-05-xcode-26-mcp-agentic-coding) — 2026 年 2 月リリースの Xcode 26.3 が Anthropic Claude Agent SDK / OpenAI Codex と連携し、Xcode 自体を MCP サーバーとして公開する仕組みを解説した実践ブログ。20 種類の組み込み MCP ツール（ファイル操作・`xcodebuild` ビルド / テスト / アーカイブ・Simulator スナップショット・SwiftUI Preview・Apple ドキュメント検索・LLDB デバッガー）が Claude Code から直接呼び出せる。「XcodeBuildMCP は Claude Code 側の MCP 設定、Xcode 26.3 の MCP サーバーは Xcode 側からの公開」という役割の違いを整理した上で両者の共存設定例も掲載。Claude Code との `.claude/settings.json` 連携手順が実務的で参考になる。

- [Xcode 26.3 + Claude Agent: What Actually Works (And What Doesn't)（marc0.dev）](https://www.marc0.dev/en/blog/xcode-26-3-claude-agent-what-actually-works-and-what-doesnt-1770494531265) — Xcode 26.3 統合の「実際に動くもの / 動かないもの」を正直にレビューした記事。SwiftUI Preview スナップショット・ドキュメント検索・テスト実行は安定している一方で、大規模プロジェクトのビルドキャッシュ連携・Instruments プロファイル取得・マルチターゲットスキームのサポートは限定的と報告。Claude Code と組み合わせた際の制限事項リストが実装前の期待値調整に役立つ。

## 💡 今日のおすすめ実践 Tip

**「macOS 27 beta 4 でシミュレーターパネルを動かす回避策と、`CLAUDE_CODE_MAX_CONCURRENT_SUBAGENTS` を使った並列ビルド制御」**

**macOS 27 beta 4 のシミュレーターパネル回避策（Issue #80472）**

macOS 27 beta 4 にアップデートしてシミュレーターパネルが起動しなくなった場合は、以下を一度実行するだけで解消できます（Metal のシェーダーキャッシュディレクトリを手動作成します）：

```bash
C_DIR=$(getconf DARWIN_USER_CACHE_DIR)
mkdir -p \
  "$C_DIR/com.anthropic.claude.ios-sim/com.apple.metal/archiveUsage.db" \
  "$C_DIR/com.anthropic.claude.ios-sim/com.apple.metalfe" \
  "$C_DIR/com.anthropic.claude.ios-sim/com.apple.gpuarchiver"
```

`/var/folders` がクリアされると無効になるため、クリーンアップ後は再実行してください。Anthropic による seatbelt プロファイル修正を待ちながら使える暫定措置です。

**`CLAUDE_CODE_MAX_CONCURRENT_SUBAGENTS` で並列ビルドを制御する（v2.1.217）**

v2.1.217 で追加された環境変数を使うと、iOS アプリを複数ターゲット（iPhone / iPad / Vision Pro）に対して並列サブエージェントでビルドする際のリソースを制御できます：

```bash
# デフォルト上限 20 を超える並列ビルドが必要な場合
export CLAUDE_CODE_MAX_CONCURRENT_SUBAGENTS=30

# 逆にリソースを節約したい場合（M1 MacBook Air 等）
export CLAUDE_CODE_MAX_CONCURRENT_SUBAGENTS=5
```

または `.claude/settings.json` 経由で設定（環境変数は `.env` 経由でも渡せます）：

```json
{
  "env": {
    "CLAUDE_CODE_MAX_CONCURRENT_SUBAGENTS": "8"
  }
}
```

デフォルトの 20 はスペックの高い Mac ではほぼ問題ありませんが、RAM 8–16 GB の環境でサブエージェントを大量に起動すると `xcodebuild` のビルドプロセスと競合してメモリ不足になる場合があります。8〜10 程度に下げると安定することが多いです。
