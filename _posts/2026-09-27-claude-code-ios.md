---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-09-27"
date: 2026-09-27 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- **[v2.1.281（2026-09-23）リリース](https://github.com/anthropics/claude-code/releases/tag/v2.1.281)** — **Mobile/Remote Control: Artifact ツールが Remote Control セッションで利用不可になっていた不具合を修正**。iOS アプリから Remote Control 経由で Claude Code を操作する際、`Artifact` ツールが欠落していた問題（09-22 以降）が解消された。あわせて MCP OAuth トークンが macOS のキーチェーンロック時に消えるバグも修正。

- **[v2.1.282（2026-09-24）リリース](https://github.com/anthropics/claude-code/releases/tag/v2.1.282)** — 主な変更点: `maxProseWidth` 設定でワイドターミナルでの Claude の出力幅上限を指定可能になった。また暗号化された Web 検索結果を含む会話でリクエストが 400 エラーになる不具合を修正。iOS 開発への直接的な変更なし。

- **[v2.1.283（2026-09-25）リリース](https://github.com/anthropics/claude-code/releases/tag/v2.1.283)** — **VSCode/Mobile: テレメトリを無効化している有料プランで Remote Control が利用不可になっていた不具合を修正**。`DISABLE_TELEMETRY=1` を設定している iOS/macOS 開発者は Remote Control が使えなかったケースがあり、今バージョンで解消される。またモデルバージョンをロックできる managed 設定 `availableModelsMatch: "exact"` および `deniedModels` が追加。

## 🛠 GitHub の動き

- **[Issue #97505（2026-09-26 作成 / OPEN）— iOS アプリ: プロジェクトに対して新規ローカルスレッドを開始できない（既存スレッドの再開か、GitHub リポ必須のクラウドセッションのみ）](https://github.com/anthropics/claude-code/issues/97505)** — iOS アプリの Code タブからは「既存ローカルスレッドの再開」または「GitHub リポジトリを紐づけた新規クラウドセッション」しか開始できず、**デスクトップで起動済みのプロジェクトに対して iOS から直接新規ローカルスレッドを開始する手段がない**。新規ローカル作業を iOS から起こせないため、デスクトップで先にセッションを立ち上げて Remote Control で引き継ぐという迂回が必要。Feature Request として、既存スレッドの再開と同様に「デスクトップで認識済みのプロジェクトに対する新規ローカルスレッド開始」を iOS アプリに追加する提案。

- **[Issue #97418（2026-09-26 作成 / OPEN）— Remote Control (iOS/Android): ツール呼び出しの前後に書いたアシスタントテキストが要約行に置き換えられる、かつ無効化する方法がない](https://github.com/anthropics/claude-code/issues/97418)** — iOS アプリから Remote Control でセッションを操作すると、**アシスタントがツール呼び出しの前または間に書いたテキストが折り畳まれて短い要約行に変換**される。生成された要約のテキストが実際のアシスタントの返答と一致しないケースも確認されており、「回答が返ってこない」と誤解されやすい。`settings.json` や CLI フラグで無効化する方法は存在せず、サーバー側フィーチャーフラグ（`tengu_classifier_summary_kill` 等）でしか制御できない。暫定回避策: ツール呼び出しをすべて先に実行し、説明テキストを最後にまとめて書く構成にする。ユーザーが iOS Remote Control を利用しつつ詳細な回答を得たい場合に直接影響する。

- **[Issue #61930（2026-09-26 クローズ）— iOS Code タブ（Dispatch composer）: 音声入力後にキーボードが送信ボタンを覆って操作不能](https://github.com/anthropics/claude-code/issues/61930)** — iOS アプリの Code タブで音声入力（ディクテーション）を使用後、ソフトウェアキーボードが展開されて下部の送信ボタンを隠してしまい、メッセージを送信できなくなっていた問題がクローズ（修正出荷と推測）。

## 📝 日本語コミュニティ

- 該当なし（2026-09-27 時点で Zenn・Qiita の新規 Claude Code × iOS / Swift / Xcode 記事は確認できず）

## 🌐 海外コミュニティ / Tips

- **[ClaudeCodeSDK（jamesrochabrun）— Swift 6.0 向け macOS 用 Claude Code 統合 SDK](https://github.com/jamesrochabrun/ClaudeCodeSDK)** — macOS アプリから Claude Code の AI 機能をプログラムで呼び出すための Swift フレームワーク。Agent SDK バックエンドと従来の CLI ヘッドレスモードの両対応で、`~/.claude/projects/` のセッションストレージへの直接アクセス・MCP サーバー対応・マルチターン会話管理などを提供。**注意点**: iOS のサンドボックス環境では `Process` API によるサブプロセス生成が禁止されているため、v2.0.0 以降は iOS サポートを削除し macOS 13+ 専用**。iOS アプリに組み込むライブラリではなく、iOS 開発を行う macOS マシン上で Claude Code を自動化・統合するための SDK として活用する想定。

## 💡 今日のおすすめ実践 Tip

**「v2.1.281/v2.1.283 の Remote Control 修正を確認し、iOS からの Artifact 生成・テレメトリ無効環境での接続を再テストする」**

今週の v2.1.281〜v2.1.283 は iOS Remote Control ユーザーに直接影響する 2 つの修正を含んでいます。

**修正① Artifact ツールが Remote Control セッションで欠落していた（v2.1.281）**

`Artifact` ツール（HTML ページ・レポートなどの公開ページ生成）が Remote Control セッションでは使えなかった問題が修正されました。iOS アプリ経由のセッションで試してみてください:

```
# Claude に依頼するだけで OK（Remote Control セッション経由でも動く）
「今日のビルドエラーを Artifact ページにまとめて」
```

**修正② テレメトリ無効で Remote Control が繋がらなかった（v2.1.283）**

CI や企業環境で `DISABLE_TELEMETRY=1` / `CLAUDE_TELEMETRY_DISABLED=1` を設定している場合、有料プランでも Remote Control が使えないケースがありました。v2.1.283 以降は修正済みです。アップデート後に iOS アプリと接続できることを確認してください:

```bash
# バージョン確認
claude --version
# → 2.1.283 以上であることを確認

# Remote Control を起動
claude remote-control
# → iOS アプリの Code タブにデバイスカードが表示されることを確認
```

**Issue #97418 の暫定回避策（iOS で詳細な回答が要約されてしまう場合）**

v2.1.283 時点でも未修正の Issue #97418 により、iOS Remote Control ではツール呼び出し前後のテキストが要約行に変換されます。詳細な出力を iOS 側で確認したい場合は CLAUDE.md に以下を追記して、ツールを先に呼び、説明を後置する構成にするよう指示する方法が有効です:

```markdown
# iOS Remote Control 向け出力ルール
- すべてのツール呼び出し（Bash / Read / Edit 等）を先に完了させてから、
  説明・結果の解釈・次のステップをまとめて最後に記述する。
- ツール呼び出しの間に長い説明を挟まない。
```

これにより「最後のテキストブロック」= 要約されない全文回答という構成になり、iOS 側でも内容が正しく表示されます。
