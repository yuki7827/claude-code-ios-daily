---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-08-19"
date: 2026-08-19 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- **v2.1.234（2026-08-17）リリース**（[Claude Code 変更ログ](https://code.claude.com/docs/en/changelog)）— v2.1.233（2026-08-14）の次のリリース。iOS 開発観点での主な変更点: ① **claude.ai 使用制限リセット時にセッションを自動継続する機能を追加** — 長時間の Xcode ビルドや実機テスト中に使用制限に達しても、制限がリセットされると自動的にセッションが再開される。`/config` でオフに設定可能; ② **Auto モードの長時間セッションでのバグを修正** — v2.1.233 でデフォルト化された Auto モードが、セッション時間が長くなるにつれて誤動作するケースを修正。archive ビルドなど長時間タスクへの信頼性が向上; ③ **GitLab MR バッジをフッター・ステータスラインに追加** — GitLab リモートを使う iOS チーム向けに、MR のステータス（draft/pending/green）がターミナル内で確認できるように; ④ **`CLAUDE_CODE_PROJECT_DIR_NAME` 環境変数を追加** — ホストごとにプロジェクトのトランスクリプト保存ディレクトリ名を短縮名で指定できるようになった（複数 iOS プロジェクトを並行管理するケースに便利）; ⑤ **セキュリティ強化: Windows NT 名前空間パス（`\??\`）をリモートファイル読み取り・セッション復元・CLAUDE.md インクルード・ワークフロースクリプトで拒否** — パス混入による意図しないファイルアクセスを防止。

## 🛠 GitHub の動き

- [Issue #87442 — iOS Simulator パネル（日本語ロケール）: "Resolution" が「解決」と誤訳、正しくは「解像度」（OPEN / 2026-08-17）](https://github.com/anthropics/claude-code/issues/87442) — macOS 26.6 Beta + Claude Desktop 1.30096.5 環境で確認。iOS Simulator パネル上部のコントロール行（フレームレート・解像度・エンコード設定）に並ぶ "Resolution" ドロップダウンのラベルが、日本語ロケール設定時に「解像度」ではなく「解決」（resolve/solve の意）と表示される翻訳ミス。**スクリーンショット付きで報告されており、再現手順が明確**。日本語環境の iOS 開発者が Simulator パネルを使う際に混乱を招く。現時点では未修正・未対応。

- [Issue #87322 — iOS Simulator パネル：物理キーボードの入力が ANSI/QWERTY レイアウトとして処理される（OPEN / 2026-08-17）](https://github.com/anthropics/claude-code/issues/87322) — フランス語 AZERTY キーボードを使用したユーザーが報告。Simulator パネル上で物理キーボードを入力すると、ホストの入力ソース（`com.apple.keylayout.French`）を無視し、キーの物理位置を US キーテーブルで解決してしまう（例: `a` キーを押すと `q` が入力される）。Simulator の `hw` 設定を変更しても症状は変わらず、パネル自身のテキストインジェクションとは別パスを辿っている可能性が高い。AZERTY・Nordic 等の非 QWERTY 環境のユーザー全般に影響する。なお同種の問題は Issue #85132（Nordic レイアウトで `@` が `"` になる問題、2026-08-08 起票）でも報告されており、根本原因が共通している可能性がある。

## 📝 日本語コミュニティ

- 該当なし（2026-08-18〜19 の範囲で確認できる新規記事なし）

## 🌐 海外コミュニティ / Tips

- [Agentic Coding in Xcode 26.3 — Part 3: Using Claude Agent Targeting iOS 18 or Later（Medium / Chinthaka Parana Widanalage）](https://medium.com/@chinthaka01/agentic-coding-in-xcode-26-3-8b62c1e46bf2) — Xcode 26.3 の Claude Agent SDK を使ったアジャイルコーディングシリーズの第3弾。iOS 18 以降のターゲット環境でエージェント型コーディングを活用する具体的な実践レポート。Part 1・2 は基本セットアップと SwiftUI への適用、Part 3 は iOS 18 固有の新 API（Foundation Models フレームワーク・On-Device AI・新コントロールスタイル等）をエージェントが自律的に活用するワークフローに焦点を当てている。Xcode 26.3 に統合された Claude Agent が `xcrun simctl`・MCP ツール・Xcode Previews を連携させながらビルドとデバッグを繰り返すパターンを詳述。全投稿での初掲載。

- [claude-code-ios-dev-guide（keskinonur / GitHub・823 ⭐）](https://github.com/keskinonur/claude-code-ios-dev-guide) — Swift/SwiftUI iOS 開発に特化した Claude Code CLI セットアップ総合ガイド。PRD ドリブン開発ワークフロー・extended thinking（ultrathink、4K〜32K トークン予算）・XcodeBuildMCP 統合・4 段階のサンドボックスパーミッションレベル（読み取り専用分析 → ビルドのみ → テストファイル変更許可 → フル開発）・プロジェクト固有スラッシュコマンド/エージェントスキル/サブエージェント/オートメーションフックの作成手順を網羅。ApptitudeLabs の `claude-code-ios-dev-setup`（2026-08-18 紹介）とは別のリポジトリで、**「フック経由の決定論的自動化」「役割特化サブエージェント」「チーム環境向け段階的パーミッション」**を重視した設計が特徴。全投稿での初掲載。

## 💡 今日のおすすめ実践 Tip

**「v2.1.234 の自動セッション継続機能 — 長時間 Xcode ビルドで使用制限に達したときの対処が楽に」**

v2.1.234 で追加された「使用制限リセット時の自動セッション継続」は、iOS 開発者にとって特に恩恵が大きい機能です。archive ビルドや CI パイプラインのセットアップ、SwiftData マイグレーション対応などのタスクは、会話が長くなりやすく、途中で使用制限（Rate Limit）に達するケースがありました。

**これまでの問題**

```
# 長時間タスク中に突然セッションが止まり、
# 「使用制限に達しました。リセットまでX分」という表示で中断
# → リセット後に手動で再起動、コンテキストを再確認してから再開
```

**v2.1.234 以降の挙動**

制限がリセットされると自動的にセッションが再開し、中断したタスクを継続します。
`/config` を開き「自動継続」をオフにすれば以前の挙動に戻せます。

**iOS 開発での活用ポイント**

長時間タスクを Claude Code に任せる際は、以下のように CLAUDE.md に「再開ポイント」の指示を書いておくと、自動継続後にエージェントが何をすべきか迷わずに再開できます:

```markdown
## タスク中断・再開のポリシー

使用制限により中断した場合、再開後は以下の順で状態を確認してください:
1. `git status` でコミット済みの状態を確認
2. `xcodebuild -list` でビルド対象を確認
3. 中断前に実行していたタスクを最初から説明してください

作業の進捗は必ず小まめに `git commit` で保存してください（1 機能 = 1 コミット）。
```

**バージョン確認**

```bash
claude --version
# 2.1.234 以上であれば対応済み

# 未満の場合はアップデート
npm update -g @anthropic-ai/claude-code
# または
claude update
```

**Issue #87442・#87322 への暫定対処（日本語・非QWERTY環境のユーザー向け）**

本日報告された 2 件の iOS Simulator パネルバグ（日本語誤訳・非QWERTY キーボードの誤マッピング）は未修正です。Simulator パネルが使いにくい場合は XcodeBuildMCP の `build`・`test` ツールを代替手段として活用することで影響を回避できます（XcodeBuildMCP はパネルとは独立したパスでビルドを実行します）:

```
# CLAUDE.md への追記例（iOS Simulator パネルのバグ回避用）
## iOS Simulator パネルのバグ（既知）
- 日本語ロケールで "Resolution" が「解決」と表示される（#87442）
- AZERTY 等の非 QWERTY 物理キーボードで入力が化ける（#87322）
パネルに問題がある場合は XcodeBuildMCP の build / test ツールを使ってください。
```
