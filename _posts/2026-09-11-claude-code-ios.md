---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-09-11"
date: 2026-09-11 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- **v2.1.268（2026-09-10）リリース**（[Claude Code 変更ログ](https://code.claude.com/docs/en/changelog)）— iOS 開発者に影響する主な変更: ① **6時間超のクラウドセッションでファイル保存が失われるバグを修正** — `[Claude Code on the web]` ルーティンを使って夜間 iOS ビルドを監視するような長時間セッションで、保存したファイル（ビルドログ・テスト結果サマリなど）が消える問題が解消（最大1日保持に変更）。夜間バッチで実機テスト結果を `.claude/` に保存していた場合に恩恵大。② **Remote Control セッションのタイトル表示バグを修正** — `claude remote-control` が起動したセッションが `ListAgents` で生成名ではなくセッションのタイトルを正しく表示するようになった。iPhone から複数セッションを管理している場合に識別しやすくなる。③ **フルスクリーンモードで Remote Control ステータスをヘッダーに移動** — プロンプトフッターに混在していた RC 接続状態がヘッダーに移動し、iPhone RC 利用時の画面レイアウトがすっきりした。④ **WebFetch の無限ハング問題を修正** — サーバーがレスポンスを閉じないまま維持するケースで WebFetch が無期限にハングする問題を修正、デフォルト300秒タイムアウトを設定（`CLAUDE_CODE_WEBFETCH_DEADLINE_MS` で上書き可能）。Apple Developer ドキュメントの大容量ページ取得時の安定性向上に寄与。⑤ **`--continue`/`--resume` の起動を高速化** — セッション再開時に SessionStart フックの完了を待たずに会話が即座に表示されるようになった。

## 🛠 GitHub の動き

- **[Issue #79991 — \[BUG\] Desktop app の iOS Simulator が Xcode 27 (Device Hub) で壊れる（anthropics/claude-code / OPEN・2026-07-22 作成）](https://github.com/anthropics/claude-code/issues/79991)** — Xcode 27 が `Simulator.app` を廃止して **Device Hub** に移行した影響で、Claude Code Desktop の iOS Simulator ツールが SimulatorKit.framework の旧パス（`/Contents/Developer/Library/PrivateFrameworks/SimulatorKit.framework`）をハードコードしていたため起動エラーになる問題。Xcode 27 では同フレームワークが `/Contents/SharedFrameworks/` に移動しており、`Error Domain=com.facebook.FBControlCore Code=0 ... it does not exist` が出る。暫定回避策: Xcode 27 より古い Xcode を `xcode-select` で指定することで Simulator 操作は復活するが、Xcode 27 SDK でのビルドは不可になるというトレードオフがある。根本的な修正はフレームワークパスを Xcode バージョンごとに動的解決する実装が必要で、修正中。**XcodeBuildMCP v2.7.0 はすでに Device Hub に対応済み**（後述）なので、Claude Code Desktop の Simulator パネルが使えない場合は XcodeBuildMCP 経由が現時点での実用的な選択肢。

## 📝 日本語コミュニティ

- **[Xcode 26.3 エージェント型コーディング入門 — Claude・Codex・MCP で開発を自動化（Qiita / kai_kou）](https://qiita.com/kai_kou/items/1b24ad62dde4c02ae4f0)** — Xcode 26.3 の AI エージェント機能（`RenderPreview` で SwiftUI スクリーンショット確認・`ExecuteSnippet` で Swift REPL 実行）と外部 Agent（Claude Code・Codex CLI）の使い分けを整理した記事。「Xcode のエージェントは UI プレビューに強く、Claude Code は長いビルドエラー修正ループに強い」という分業が実践例とともに示されている。

- **[【初心者向け】VS Code の Claude Code で「iOS コードレビュー → 修正 → Xcode ビルド確認」を自動化する 第1弾・第2弾（Qiita / 4q_sano）](https://qiita.com/4q_sano/items/f29dba5fa92958850a32)** — `xcrun mcpbridge` 経由で Claude Code に Xcode MCP を接続し、VS Code 上でコードレビュー→修正→ビルド確認→テスト項目作成の一連のフローを自動化する実践チュートリアル連載。第2弾（[URL](https://qiita.com/4q_sano/items/effe51de5e9654777f1e)）ではテスト項目の自動生成まで拡張されており、iOS 開発初心者向けにステップバイステップで説明されている。

## 🌐 海外コミュニティ / Tips

- **[XcodeBuildMCP v2.7.0 リリース — Xcode 27 Device Hub 対応完了（github.com/getsentry/XcodeBuildMCP / Releases）](https://github.com/getsentry/XcodeBuildMCP/releases)** — UI オートメーションツールが Xcode 27 の新しいシミュレーター管理システム「Device Hub」に完全対応。旧 `Simulator.app` 環境（Xcode 26 以前）との後方互換性も維持。その他の主な変更: ①ビルド・テストツールが schema v3 を返すようになった（構造化テスト結果で prepared-test パスをサポート）、②スキーム未指定時のビルドがデフォルトで Debug ではなくスキームの設定に従うよう変更（**破壊的変更**：明示的に Debug が欲しい場合は `--configuration Debug` を指定すること）、③`xcodebuildmcp purge` コマンドの追加（ワークスペースストレージ管理）。

## 💡 今日のおすすめ実践 Tip

**「Xcode 27 環境での iOS シミュレーター戦略：Claude Code Desktop と XcodeBuildMCP の現在地」**

Xcode 27 では Apple がシミュレーター管理を `Simulator.app` から **Device Hub** へ移行しました。この変更により、Claude Code Desktop の iOS Simulator パネル（Issue #79991）が現時点では動作しない一方、XcodeBuildMCP v2.7.0 は Device Hub に対応済みです。両者を正しく使い分ける設定を紹介します。

**現状の整理（2026-09-11 時点）**

| 機能 | Claude Code Desktop Simulator パネル | XcodeBuildMCP v2.7.0 |
|------|--------------------------------------|----------------------|
| Xcode 27 対応 | ❌ 未対応（Issue #79991 対応中） | ✅ 完全対応 |
| Xcode 26 以前 | ✅ 動作 | ✅ 後方互換あり |
| UI オートメーション | ✅ | ✅（`wait_for_ui`, `batch`, `drag` 追加） |
| Claude Code との統合 | ネイティブ統合 | MCP 経由 |

**Xcode 27 環境での推奨設定（CLAUDE.md）**

```markdown
## シミュレーター操作
- Xcode 27 を使用しているため、iOS Simulator パネルは使用不可
- シミュレーター操作はすべて XcodeBuildMCP 経由で行うこと
- ビルド・テスト: xcodebuildmcp build / test ツールを使用
- UI テスト: xcodebuildmcp の ui_tap / ui_type ツールを使用
- スキーム未指定ビルドは v2.7.0 からデフォルトが Debug → スキーム設定に変更
  明示的に Debug ビルドが必要な場合は configuration: "Debug" を指定すること
```

**XcodeBuildMCP を最新版に更新する**

```bash
# XcodeBuildMCP CLI でアップデート確認・適用
xcodebuildmcp upgrade

# または npx 経由でバージョン確認
npx xcodebuildmcp --version
# 2.7.0 以上であることを確認
```

Claude Code Desktop の Simulator パネル修正（Issue #79991）がリリースされたら CLAUDE.md の制限を外すことを忘れずに。
