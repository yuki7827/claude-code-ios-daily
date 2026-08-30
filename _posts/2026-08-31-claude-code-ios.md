---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-08-31"
date: 2026-08-31 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- 該当なし（2026-08-31 時点で v2.1.251〈2026-08-28〉以降の新規リリースは確認できず。[Claude Code 変更ログ](https://code.claude.com/docs/en/changelog)を参照）

## 🛠 GitHub の動き

- [Issue #88217 — iOS Simulator MCP: `attach` が報告したデバイスとは別のシミュレータにタップ・スクリーンショットが飛ぶ／シャットダウン済みデバイスを「接続済み」と誤報告／削除済みデバイスがパネルに固着する（3 バグ一括報告、OPEN / 2026-08-20 起票・2026-08-30 更新）](https://github.com/anthropics/claude-code/issues/88217) — 2026-08-27 でカバーした Issue #89826（マルチセッション時の UDID 混線）とは独立した、シングルセッション・クリーン環境でも再現する 3 つのバグを網羅したレポート。① `attach {udid: X}` が成功を報告するが、直後の `screenshot`・`tap` は X ではなく別デバイスに向く（`attach` は set ではなく append 動作のため、複数回 attach するとデフォルトターゲットがずれる）、② シャットダウン済みデバイスへの `attach` が「already attached」＋座標空間を返しつつデバイスを起動せず、`screenshot` は `No Image available to encode` で失敗（パネルの「シャットダウン済みは自動起動」という文言と矛盾）、③ 削除済みデバイスがパネルに固着し、有効な UDID で再 `attach` しても解消しない（`detach` → `attach` のみが回復手段）。**暫定回避策**: `screenshot`・`tap`・`swipe` のすべてに常に `udid` を明示的に渡す。

- [Issue #90707 — iOS Simulator ライブパネルが macOS 27.0 beta で Metal `recordBinaryArchiveUsage` nil-array クラッシュループ（OPEN / 2026-08-30 起票）](https://github.com/anthropics/claude-code/issues/90707) — macOS 27.0 beta (26A5406e)・Claude Desktop 1.40609.0 環境で、`claude-ios-sim` ヘルパーが CoreImage の `CIContext` 初期化時に必ずクラッシュする新規報告。`attach` は成功するが直後の `screenshot` で「restarting after a crash」ループ。クラッシュシグネチャは既出の #88504 / #80177 と同一（Metal の `recordBinaryArchiveUsage` が `nil` 要素を `NSArray` に挿入）だが、こちらは **SDK 26.1 でビルドされたヘルパーが macOS 27 beta で特定の linked-on-SDK 動作ゲートに引っかかる**という仮説を立てており、seatbelt プロファイルの deny とは別の側面から分析されている。暫定回避策: `xcrun simctl io booted screenshot` で headless 取得。

- [Issue #90546 — iOS Simulator サイドカーがフレームレンダリングで毎回 SIGABRT（`recordBinaryArchiveUsage` のシェーダーキャッシュ nil）— 根本原因の特定とクローズ（CLOSED / 2026-08-29）](https://github.com/anthropics/claude-code/issues/90546) — 既出の Metal クラッシュ群（#80177 / #88504）の詳細な根本原因分析が行われ、2026-08-29 にクローズ（重複/情報提供として処理された模様）。起票者はアセンブリ逆アセンブルで `Metal が getShaderCachePath() = nil を NSArray に挿入`する経路を特定し、`claude-ios-sim.sb`（seatbelt プロファイル）が `DARWIN_USER_CACHE_DIR`（Metal のシェーダーキャッシュディレクトリ）への書き込みを禁じているため `nil` が返ることを `sandbox-exec` で実証した。提案修正は `(allow file-write* (subpath (param "DARWIN_CACHE")))` 1 行の追加。また副次的なバグとして、Xcode-beta.app しかインストールされていない環境でセットアップウィザードの Xcode チェックが `/var/db/xcode_select_link` 実体のみを参照するため、`xcode-select -p` では正しく解決できているのにウィザードが止まる問題も報告されている。

- [Issue #90684 — iOS Simulator パネルが macOS 27.0 beta + Xcode 27 beta で Metal/CoreImage クラッシュループ（OPEN / 2026-08-30 起票）](https://github.com/anthropics/claude-code/issues/90684) — Xcode 27.0 beta (27A5209h)・iOS 27.0 シミュレータランタイム環境での報告。クラッシュシグネチャは他の macOS 27 beta 報告と同一。シミュレータ自体は健全（`simctl` のビルド・起動・スクリーンショットは正常）なため MCP 経由の headless 操作は引き続き利用可能。合計 6 件のクラッシュレポートが 2 分以内に生成されたことから、`attach` → 即クラッシュのループが確定的に再現する。

## 📝 日本語コミュニティ

- [XcodeBuildMCP × Claude Code スキルシステムで iOS ビルドを自動化する（Zenn / kyoichi）](https://zenn.dev/kyoichi/articles/claude-code-xcodebuildmcp-ios-build) — XcodeBuildMCP のスキルシステムと Claude Code を組み合わせた iOS ビルド自動化の実践記事。MCP ツールとスキルを組み合わせて xcodebuild の実行・エラー解析・修正までをエージェントに委ねるワークフローを解説している。

- [Claude Code から Xcode が提供する MCP に接続する方法（Zenn / pepabo）](https://zenn.dev/pepabo/articles/4c0d1019ac1f7d) — Xcode 26.3 の `mcpbridge` を Claude Code CLI から利用する接続手順を解説した記事。mcpbridge 経由で `RenderPreview`・`BuildProject`・`ExecuteSnippet`・`RunSomeTests` などの Xcode ネイティブ MCP ツールを Claude Code から呼び出す設定方法をまとめている。

- [Xcode の Claude Agent・Codex が settings.json・config.toml でセットアップできるように（Zenn / treastrain）](https://zenn.dev/treastrain/articles/bc9435a36fe498) — Xcode 26.5 以降の Enterprise Configuration で、Claude Agent 向け `settings.json` と Codex 向け `config.toml` を使って Amazon Bedrock・Google Vertex AI・Microsoft Foundry への接続が設定できるようになったことを解説。エンタープライズ iOS チームが managed configuration として全員に配布するユースケースに適している。

## 🌐 海外コミュニティ / Tips

- [xclaude-plugin（conorluddy / GitHub、182 stars）](https://github.com/conorluddy/xclaude-plugin) — iOS 開発の Claude Code プラグインで、**トークン効率を最優先**に設計された 8 つのモジュール式 MCP サーバー集。Xcode・シミュレータ・IDB ツールを 24 個の共有ツール定義から重複なく提供し、ワークフローに必要な MCP だけを有効化できる設計が特徴。代表的なサーバー: `xc-build`（ビルド検証、約 600 トークン）・`xc-launch`（シミュレータ起動、約 400 トークン）・`xc-interact`（UI 操作テスト、約 900 トークン）。全機能入りの `xc-all`（約 3500 トークン）と比べて用途別 MCP は最大 83% のトークン削減が可能。アクセシビリティファーストの UI 自動化により、スクリーンショット比で 3〜4 倍高速・80% 安価な UI テストを実現している点も注目。

- [ClaudeCodeSDK（jamesrochabrun / GitHub、98 stars）](https://github.com/jamesrochabrun/ClaudeCodeSDK) — macOS アプリから Claude Code をプログラムで制御する Swift SDK。ヘッドレスモードと Agent SDK バックエンドの 2 種類のバックエンドを切り替え可能で、マルチターン会話・ストリーミング JSON・MCP 対応・セッション管理・レート制限ハンドリングを備えている。Claude Code CLI（`npm install -g @anthropic-ai/claude-code`）が前提のため macOS 専用（iOS 直接動作には非対応）だが、Swift Package Manager 経由でインストールでき、iOS 開発ツールを Claude Code セッションと連携させる macOS アプリの作成に有用。

## 💡 今日のおすすめ実践 Tip

**「Issue #88217 の暫定回避策：iOS Simulator MCP は全ツール呼び出しで `udid` を明示する」**

Issue #88217 は、`attach` の成功報告が実際のデフォルトターゲット設定を保証しないという根本的な問題を明らかにしました。修正が入るまでの間、**すべての MCP ツール呼び出しに `udid` を明示的に渡す**ことが唯一の確実な回避策です。

**問題になるパターン（`attach` 後に udid を省略する）**

```
# ❌ attach で UDID を指定しても、後続の操作でターゲットがずれる可能性がある
control { action: "attach", udid: "AAAA-..." }
control { action: "screenshot" }              # ← 別のデバイスが返る場合がある
control { action: "tap", x: 100, y: 200 }    # ← 別のデバイスに飛ぶ場合がある
```

**安全なパターン（全操作に udid を明示）**

```
# ✅ すべての呼び出しに udid を渡す
SIM_UDID="AAAA-1234-..."
control { action: "attach",     udid: "$SIM_UDID" }
control { action: "screenshot", udid: "$SIM_UDID" }
control { action: "tap",        udid: "$SIM_UDID", x: 100, y: 200 }
control { action: "swipe",      udid: "$SIM_UDID", startX: 100, startY: 400, endX: 100, endY: 200 }
```

CLAUDE.md にこのルールを明記しておくと、エージェントが自律的に従います:

```markdown
## iOS Simulator MCP の使い方

- `attach` で接続した UDID を変数として保持し、以降のすべてのツール呼び出し
  （`screenshot`・`tap`・`swipe`・`input`）に `udid` パラメータを必ず渡すこと。
- `udid` を省略した呼び出しは現時点では信頼できないため使用しないこと。
- デバイスを切り替える際は `detach` → `attach` の順で行うこと（`attach` の重ね掛けは不可）。
```

**シャットダウン済みデバイスへの attach 前に boot を明示する**

Issue #88217 の 2 番目のバグとして、シャットダウン済みデバイスへの `attach` が成功を返しつつデバイスを起動しない問題も確認されています。`attach` の前に必ず `xcrun simctl boot` を実行するか、`xcrun simctl list devices | grep Booted` で起動確認を挟んでください:

```bash
# シミュレータが Booted であることを確認してから attach する
SIM_UDID=$(xcrun simctl list devices booted -j | \
  python3 -c "import sys,json; devs=[d for rt in json.load(sys.stdin)['devices'].values() for d in rt if d['state']=='Booted']; print(devs[0]['udid']) if devs else sys.exit(1)")

if [ -z "$SIM_UDID" ]; then
  xcrun simctl boot "iPhone 16 Pro"  # 起動してから UDID を取得し直す
fi

# その後 attach
```

**macOS 27 beta のライブパネル問題は引き続き未修正**

Issue #90707・#90684・#80177 は引き続き OPEN です。macOS 27 beta 環境では `xclaude-plugin` の `xc-build`（headless ビルド）や `xcrun simctl io booted screenshot` を代替として使いつつ、Anthropic の修正リリースを待つ形になります。
