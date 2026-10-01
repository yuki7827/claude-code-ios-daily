---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-10-02"
date: 2026-10-02 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- **[v2.1.287（2026-10-01）リリース](https://github.com/anthropics/claude-code/releases/tag/v2.1.287)** — **Claude Mods** 機能が追加。プラグインがエージェントの動作を深いレベルで変更できるようになった。組み込みモッド「You should know」が同梱（テレメトリ有効時に `/plugin enable cc-plugin-you-should-know@builtin` で有効化）。**Agent ビューに `n:<text>` フィルターを追加**：セッション名とタスクをキーワードで絞り込み、Enter キーで最初の一致を即座に開ける。**Remote Control の無限ハング修正**：再接続リクエストへの応答がない場合、30 秒後に自動で中止してリトライするよう変更（Remote Control セッションが無応答のまま永続していたケースへの対処）。Opus 5.5 ↔ Sonnet 5.5 切り替え時に MCP ツール通知が上書きされるバグも修正。フック処理でスクリプトファイルが見つからない場合の無限ループ問題を修正。

- **[ClaudeForFoundationModels v0.2.2（2026-09-28）](https://github.com/anthropics/ClaudeForFoundationModels/releases/tag/v0.2.2)** — WWDC 2026 で発表された Apple Foundation Models 対応 Swift パッケージに **claude-sonnet-5-5 モデルを追加**。同パッケージは iOS 27 / macOS 27 / visionOS 27 / watchOS 27 以降を対象とし、Apple の `LanguageModel` プロトコルに準拠。**アプリのコードをほぼ変えずにオンデバイス Apple モデルと Claude を引数一つで切り替えられる**。v0.2.0 で Fable 5 / 5.1、v0.2.1 で Opus 5.5、今回 v0.2.2 で Sonnet 5.5 が加わり、最新モデル群が揃った。

## 🛠 GitHub の動き

- **[Issue #98767（2026-10-01 作成 / OPEN）— iOS アプリがアシスタントの単純返答を Remote Control セッション内で 2 回表示する](https://github.com/anthropics/claude-code/issues/98767)** — `/compact` 後に再開した長期 Remote Control セッションで、ツール呼び出しを含まない単純テキスト返答が iOS アプリ上に **2 回連続で表示される** バグ（CLI 側の送信は 1 回のみ、Opus 5.5 使用時に確認）。iOS からセッション進捗を監視しているケースで混乱を招く。ラベル: `bug`, `area:ui`, `platform:ios`。

- **[Issue #97830（2026-09-28 作成 / OPEN）— Xcode 27.0 の Claude Agent: v2.1.280 以降が起動後 40ms 以内に exit code 1 で終了し「thinking」のまま永続ハング（v2.1.220 は正常動作）](https://github.com/anthropics/claude-code/issues/97830)** — Xcode 27.0（27A266a）/ macOS 26 Apple Silicon 環境で Claude Agent を v2.1.280 に更新すると、Xcode の最初の stdin 書き込みから 40ms 以内にプロセスが終了しセッションが「thinking」のままになる。ターミナルからは同一バイナリが正常起動するため Xcode 側 stdin フォーマットの変更が原因と推定。**暫定回避策: `npm install -g @anthropic-ai/claude-code@2.1.220` で旧版に固定**。ラベル: `bug`, `area:ide`, `has repro`, `regression`。

## 📝 日本語コミュニティ

- 該当なし（2026-10-02 時点で Zenn・Qiita の新規 Claude Code × iOS / Swift / Xcode 記事は確認できず）

## 🌐 海外コミュニティ / Tips

- **[ClaudeForFoundationModels — Apple Foundation Models フレームワーク統合の概要（Anthropic ドキュメント）](https://platform.claude.com/docs/en/cli-sdks-libraries/libraries/apple-foundation-models)** — Apple が WWDC 2026 で公開した `LanguageModel` プロトコルに Anthropic が準拠した Swift パッケージ。`@Generable` アノテーションによる型付き Swift 出力、ストリーミング、ツール呼び出し、SwiftUI ビューへの構造化レスポンス返却に対応。iOS アプリでオンデバイスモデルの能力を超えるタスクを Claude にオフロードするユースケースで特に有効。Apache-2.0 ライセンスのオープンソース、ただし Anthropic API キーが必要。

## 💡 今日のおすすめ実践 Tip

**「Xcode 27.0 + Claude Agent v2.1.280 以降でハングする場合のバージョン固定と Issue 追跡（Issue #97830 対応）」**

Xcode 27.0 上で Claude Agent が `thinking` のまま永続する場合、まず以下で旧版に切り替えてください：

```bash
# 動作が確認されている旧版に固定
npm install -g @anthropic-ai/claude-code@2.1.220

# インストール確認
claude --version
```

Xcode 26 以前の環境またはターミナル上では v2.1.280+ でも正常動作するため、**プロジェクト単位で使用 Xcode バージョンを固定する** 方法も有効です：

```bash
# Xcode 27 と 26 を共存させてプロジェクトに応じて切り替え
sudo xcode-select -s /Applications/Xcode_26.app

# Claude Agent を使うターミナルセッションで確認
xcodebuild -version
claude --version
```

Issue #97830 に影響を受けている方はバグトラッカーで状況を追うか、修正版がリリースされるまで上記の回避策を継続してください。
