---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-08-05"
date: 2026-08-05 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- **v2.1.221（2026-08-04）リリース**（[Claude Code 変更ログ](https://code.claude.com/docs/en/changelog)）— 08-04 記事では v2.1.220 が最新とされていたが翌日リリース。iOS 開発者向け主要変更: ① **Focus View**（`Ctrl+Alt+F`）— ツール活動をターンごとの折りたたみ可能な要約の後ろに隠しライブ実行インジケーターを表示。XcodeBuildMCP の長いビルドセッションで画面が埋もれる問題に効く; ② **サンドボックス認証情報マスキング**（`mode: "mask"`、Linux/WSL）— CI 上の Keychain 相当ファイルをサンドボックス外へ流出させずマスク。TestFlight 配信 CI など Linux runner を使う場合に有用; ③ **`-p`（print）モードでの MCP サーバー接続 fix** — 初回ターン前に MCP サーバーが接続されない問題を修正。fastlane 等を MCP 経由で呼び出す自動化スクリプトで影響していた。

## 🛠 GitHub の動き

- [Issue #83759 — 複数 Mac 環境で共有 OAuth 認証情報がローテーションされると Remote Control セッションが自動アーカイブされる（OPEN / 2026-08-04）](https://github.com/anthropics/claude-code/issues/83759) — 開発 Mac と CI Mac など 1 つの Claude サブスクリプションを複数台で共有している場合、一方で OAuth 認証情報が更新されるともう一方の Remote Control セッションがサーバー側で自動アーカイブされる問題。iPhone の Code タブに「This session is archived」バナーが表示され継続できなくなる。データロスなし（ローカル JSONL は残る）。**回避策:** `claude.ai/code/<session_id>` を直接開いて「Unarchive」をクリック。`disableAutoArchive` フラグの追加が要望されている。

- [Issue #83790 — 1 クリックでセッションを新しい関連セッションへ引き継ぐ機能（FEATURE REQUEST / 2026-08-04）](https://github.com/anthropics/claude-code/issues/83790) — あるセッションを終了し、次の関連セッションをスレッドとして紐付けてセッション一覧に表示する機能のリクエスト。iOS 開発で「設計セッション → 実装セッション → レビューセッション」と複数セッションにまたがる作業が多い場合に有用な提案。

## 📝 日本語コミュニティ

- [Claude Code のスキルで個人開発アプリの iOS リリースを楽にした話（Qiita / _it_ / 2026-06-19）](https://qiita.com/_it_/items/45127d2e41cafff8477c) — React Native + Expo アプリのリリースノート作成・App Store メタデータ入力・提出フローを Claude Code の Skills で半自動化した実践例。fastlane を使わずに Skills と `CLAUDE.md` の組み合わせで再現性を担保する設計が参考になる。iOS 個人開発者がリリース作業を Claude Code に任せる最初の一歩として読む価値あり。

## 🌐 海外コミュニティ / Tips

- [iPad as Your AI Coding Cockpit: Running Claude Code, Codex, and Amp Remotely（Tactic Remote Blog / 2026-05-26）](https://tacticremote.com/blog/2026-05-26-claude-code-on-ipad-ai-coding-cockpit/) — Mac 上の Claude Code セッションに Tactic Remote 経由でプロンプトを投げ・出力を確認・アクション承認する構成を iPad から行う方法を解説。iOS Simulator を Mac 上で動かしながら iPad で指示を出す「iOS 開発専用コックピット」的な使い方が紹介されており、デスクと別の場所からビルド状況を監視しながら追加指示を送るシナリオに適している。

## 💡 今日のおすすめ実践 Tip

**「v2.1.221 の Focus View を XcodeBuildMCP 長時間セッションで活用する」**

v2.1.221 で追加された **Focus View**（`Ctrl+Alt+F`）は、XcodeBuildMCP を使った iOS ビルド・テストの長時間セッションで特に効果的です。

**なぜ iOS 開発に有効か**

XcodeBuildMCP でビルドを回す際、ビルドログ・テスト出力・Simulator スクリーンショットなど膨大なツール活動がチャット画面に流れ込み、進捗や会話の流れが埋もれがちです。Focus View を有効にすると、ツール活動はターン単位の折りたたみ可能な要約（ライブ実行中インジケーター付き）に畳まれ、必要なときだけ展開できます。

**有効化手順**

1. VS Code の Claude Code 拡張でセッションを開く
2. チャットメニュー（`...`）から「Toggle Focus view」を選ぶか `Ctrl+Alt+F` を押す
3. 以降のセッションでも設定が維持される

**Issue #83759 の副次対策として**

Remote Control セッションが予期せずアーカイブされた際に「どこまで作業が進んでいたか」が Focus View のターン要約一覧から把握しやすくなります。Unarchive して再開したときに、各ターンの折りたたみ要約をたどることで作業文脈を素早く復元できます。

**CLAUDE.md への追記例**

```
## XcodeBuildMCP セッション方針
- 長いビルド・テストセッションは Focus View を有効にして実行する
- ターンごとのツール要約からビルド成否を確認し、詳細は必要時のみ展開する
- Remote Control を iOS から使う場合も、Focus View により概要が把握しやすくなる
```
