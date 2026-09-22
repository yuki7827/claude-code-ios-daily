---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-09-23"
date: 2026-09-23 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- **[v2.1.280（2026-09-22）リリース](https://code.claude.com/docs/en/changelog)** — 主な変更点: ① **Claude Opus 5.5**（`claude-opus-5-5`、コンテキスト 1M トークン）が新しいデフォルト Opus モデルとして追加（Pro / Team Standard プランのデフォルトも Sonnet から Opus に変更）。② **`CLAUDE_CODE_MAX_MCP_DESCRIPTION_LENGTH` 環境変数を追加** — セッション内のすべての MCP ツール説明・サーバー命令の 2,048 文字上限を変更可能に（XcodeBuildMCP の 82 ツールや Xcode ネイティブ MCP の詳細な tool description を拡張したい場合に有用）。③ iOS Simulator ヘルパー（`claude-ios-sim`）の macOS 27 クラッシュループ修正が含まれる見込み（後述の GitHub 動き参照）。その他多数のバグ修正（サブエージェント・MCP・シンボリックリンク・自動モードなど）。

## 🛠 GitHub の動き

- **[Issue #87322（2026-08-17 作成 / OPEN）— iOS Simulator パネル: 物理キーボード入力が ANSI/QWERTY として扱われ、非 QWERTY 配列では入力が文字化け（French AZERTY など）](https://github.com/anthropics/claude-code/issues/87322)** — iOS Simulator パネルが各キーの物理位置を US テーブルに照合するため、ホストの Input Source（`com.apple.keylayout.French` 等）を無視する。French AZERTY でキー `a` を押すと `q` が入力されるなど、英語以外のキーボードを持つ開発者に広く影響する。シミュレーター側のハードウェアレイアウト設定を変えても症状が変わらないことからパネル側の問題と特定されている。なお、ソフトウェアテキスト注入側の同種の問題は Issue #85132（2026-09-22 記事掲載）として別途 OPEN。

- **[Issues #88504 / #90684 / #90707 / #93580 / #94554 がまとめてクローズ（2026-09-22）](https://github.com/anthropics/claude-code/issues/94554)** — macOS 27 で iOS Simulator ヘルパー（`claude-ios-sim`）が Metal の CoreImage 初期化中に SIGABRT でクラッシュループし続けた一連の報告が v2.1.280 リリースと同日（2026-09-22 19:00 UTC ごろ）に同時クローズ。根本原因はサンドボックスプロファイル（`claude-ios-sim.sb`）が Metal のバイナリアーカイブ使用記録に必要な `DARWIN_USER_CACHE_DIR` への書き込みを拒否していたこと（#88504 で最小再現コードと `(allow file-write* (subpath (param "DARWIN_CACHE")))` 一行追記での修正確認済み）。クローズのタイミングから v2.1.280 にこの修正が含まれたと推測。macOS 27（GA）で iOS Simulator パネルが動作しなかったユーザーは v2.1.280 へのアップデートを推奨。

- **[Issue #96126（2026-09-22 作成 / OPEN）— v2.1.280 のリグレッション: PermissionRequest フックが自動承認したプロンプトでも Remote Control の push 通知が Claude iOS アプリへ誤送信される](https://github.com/anthropics/claude-code/issues/96126)** — v2.1.270 以前は PermissionRequest フックが `decision.behavior: "allow"` を返した場合に push は発火しなかったが、v2.1.280 からフック実行より前（プロンプトが開いた瞬間）に push が送出されるようになった。Remote Control + PermissionRequest フック（`Write|Edit|Bash|mcp__.*` を自動許可）を使って macOS 上でコーディングしながら iOS アプリへ通知を受け取っている開発者は、フック承認済みのプロンプトに対しても「権限確認が必要」通知が届くようになる。セッションを開くと何も待機していない空振り状態になる点が確認されている。

## 📝 日本語コミュニティ

- 該当なし（2026-09-23 時点で新規の Zenn・Qiita 記事は確認できず）

## 🌐 海外コミュニティ / Tips

- 該当なし（2026-09-23 時点で新規の注目記事・投稿は確認できず）

## 💡 今日のおすすめ実践 Tip

**「v2.1.280 で macOS 27 の iOS Simulator パネルが復活した可能性 — アップデートと動作確認の手順」**

2026-08 後半〜09 月にかけて、macOS 27 へアップグレードした環境では iOS Simulator パネル（`claude-ios-sim`）が `attach` のたびに SIGABRT でクラッシュループし、tap/swipe/screenshot が完全に動かない状態が続いていました。v2.1.280 リリースと同日に 5 件の関連 Issue が同時クローズされ、サンドボックスプロファイルの修正が出荷されたと見られます。

**ステップ 1: v2.1.280 以上へアップデートする**

```bash
# npm 経由でインストールしている場合
npm update -g @anthropic-ai/claude-code

# バージョン確認
claude --version
# → 2.1.280 以上であることを確認
```

Claude Desktop アプリ経由の場合はアプリを再起動して最新版に更新してください。

**ステップ 2: iOS Simulator パネルの動作確認**

```bash
# シミュレーターを起動しておく
xcrun simctl boot "iPhone 17 Pro"

# Claude を起動し、パネルでシミュレーターをアタッチ
# （または MCP ツールから直接テスト）
```

以下が正常動作のサイン:
- パネルが開きシミュレーター画面が表示される
- `screenshot` が実際の画面を返す
- `~/Library/Logs/DiagnosticReports/` に新しい `claude-ios-sim-*.ips` が生成されない

**ステップ 3: まだクラッシュする場合は確認する**

```bash
# クラッシュログのスタックを確認
cat $(ls -t ~/Library/Logs/DiagnosticReports/claude-ios-sim-*.ips 2>/dev/null | head -1) \
  | grep -A 10 "lastExceptionBacktrace"
```

引き続き `recordBinaryArchiveUsage:` スタックで落ちる場合は Issue #94554 へのコメントか新規 Issue として報告してください。

**あわせて: `CLAUDE_CODE_MAX_MCP_DESCRIPTION_LENGTH` を XcodeBuildMCP 環境に設定**

v2.1.280 で追加された同環境変数は、XcodeBuildMCP の 82 ツールを使う環境でツール説明が 2,048 文字で切れていた場合の対処になります。

```bash
# .bashrc / .zshrc または CI の環境変数に追加
export CLAUDE_CODE_MAX_MCP_DESCRIPTION_LENGTH=4096
```

XcodeBuildMCP や Xcode ネイティブ MCP を併用していてツールの呼び出し精度が低いと感じていた場合、説明が途中で切れていたことが原因の可能性があります。
