---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-08-03"
date: 2026-08-03 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- 新リリースなし（v2.1.220（2026-07-25）が最新。2026-07-26 記事で取り上げ済み）
- **Claude Opus 4.1（claude-opus-4-1-20250805）が 2026-08-05 に API 退役**（[Claude Platform Release Notes](https://platform.claude.com/docs/en/release-notes/overview)）— 移行先は Claude Opus 4.8。Claude Code が内部で使う SDK モデルの自動切り替えは Anthropic 側が対応済みだが、iOS アプリ・バックエンドで claude-opus-4-1-20250805 をハードコードして直接 API 呼び出しをしている場合は 08-04（UTC 深夜）までに移行しないとリクエストが失敗し始める。XcodeBuildMCP 経由のビルド自動化ではなく Claude API を直接叩いている CI スクリプトがある場合は今日中に確認を。

## 🛠 GitHub の動き

- [Issue #83378 — Remote Control 再アーミング時に死んだ bridge handle が削除されず、iOS の Code タブに重複セッション行が蓄積（OPEN / 2026-08-02）](https://github.com/anthropics/claude-code/issues/83378) — Claude Desktop を再起動して `/remote-control` を手動で実行すると、古い（切断済み）bridge handle がサーバー側の `bridgeSessionIds` 配列から削除されないまま新しい handle が追記される。結果として iPhone の Code タブに「Disconnected」行と「Connected」行の 2 行が表示され、再起動のたびに行が増え続ける。報告者は回避策も詳細にまとめており、現状は「問題のある Desktop セッションをアーカイブ / 削除してから再作成する（ただし閲覧中のセッションごと消える）」しかない。08-01 記事で取り上げた Issue #82140（再アーミング自体が動かないバグ）とは根本原因が異なり、修正を組み合わせないと完全解消しないとも指摘されている。

- [Issue #83283 — iOS アプリ Code タブ: 入力フィールドにフォーカス後の最初の文字が強制コミットされ、日本語 IME 変換が壊れる（OPEN / 2026-08-02）](https://github.com/anthropics/claude-code/issues/83283) — iPhone の Code タブ（Remote Control セッション）で日本語 12 キーかなフリック入力をすると、フィールドをタップして最初に入力した文字だけ未確定状態（下線）にならず即コミットされる。濁点キー（小゛゜）が顔文字キー（^_^）に化けるため「ど」を打とうとすると「と^_^」が入力される。報告者が発見した**回避策: 一度 Backspace で全消去し、フィールドをタップし直さず（フォーカスを外さず）そのまま再入力すると 2 文字目以降は正常に変換できる**。原因はフォーカスイベント時に React Native の TextInput `value` が再代入されて iOS の `markedTextRange`（変換中テキスト）がクリアされることと疑われており、中国語・韓国語など IME ベースの言語にも同様の影響があると見られる。

- [Issue #79991 — Desktop アプリの iOS Simulator が Xcode 27（Device Hub）環境で動かない（CLOSED / 2026-08-02）](https://github.com/anthropics/claude-code/issues/79991) — **修正済み**。07-22 に報告された Xcode 27 beta 環境での iOS Simulator 統合クラッシュが 2026-08-02 にクローズ。08-01 記事の Issue #82864（Metal スクリーンキャプチャパスのクラッシュループ）と同系統の問題だが、こちらは Device Hub 対応の本体修正が入った。Claude Desktop を最新にアップデートすれば Xcode 27 の Device Hub でも iOS Simulator ペインが正常動作するはず。

## 📝 日本語コミュニティ

- [Claude Code に iOSアプリの E2Eテスト までやらせるようにした（iOSシミュレータ + AXe）（Qiita / peka2 / 2026-07-19）](https://qiita.com/peka2/items/9ce150b3b480516fc16e) — XcodeBuildMCP の AXe（アクセシビリティツリー操作ライブラリ）を使って、Claude Code にビルド → シミュレーター起動 → UI タップ → スクリーンショット撮影 → 結果検証の一連をすべて自動で回させる構成を解説。単なる UI 確認にとどまらず**E2E テスト**として成立させるための CLAUDE.md 設定（テスト失敗時の再試行戦略、タップ座標ではなくアクセシビリティ識別子でボタン指定させるルール）が参考になる。

## 🌐 海外コミュニティ / Tips

- [XcodeBuildMCP に `xcodebuildmcp upgrade` コマンドとプラットフォーム別セットアップウィザードが追加（xcodebuildmcp.com）](https://www.xcodebuildmcp.com/docs/changelog) — `xcodebuildmcp upgrade --check` でアップデートの有無を確認し、`--yes` で非対話的にアップグレードが完了。ウィザードは起動時に macOS / iOS / tvOS / watchOS / visionOS を選択すると、プラットフォームに不要な Simulator / デバイス設定の質問がスキップされ、最小構成ですばやくセットアップできる設計。XcodeBuildMCP を複数の iOS プロジェクトにまたいで管理している場合、`--check` を CI の定期チェックジョブに入れておくとバージョン管理が楽になる。

## 💡 今日のおすすめ実践 Tip

**「iOS の Code タブで日本語 IME が壊れたときの回避手順（Issue #83283 暫定対策）」**

Issue #83283 が示す通り、現行の Claude iOS アプリ（Code タブ・Remote Control セッション）では、フィールドへのタップ（フォーカス取得）直後に入力した最初の 1 文字が強制コミットされ、日本語フリック入力でよく使う濁点操作（`小゛゜` キー）が効かなくなります。

**その場の回避手順（3 ステップ）:**

1. フィールドをタップしてフォーカスを当てる
2. Backspace を長押しするか連打して、フィールドを完全に空にする（ダミー文字を入れたなら消す、最初から空なら何もしない）
3. **フィールドをタップし直さずそのまま**入力を再開する → 最初の文字から正常に変換候補が出る

フォーカスを外さず（タップし直さず）再入力する、という点がポイントです。フィールドを一度でも再タップすると再び同じ問題が起きるため、誤タップに注意してください。

**Claude Code 側での暫定策（CLAUDE.md に追記）:**

Remote Control 経由でフィードバックを送ることが多い場合、CLAUDE.md に「日本語でのフィードバック入力は Apple 純正の音声入力（マイクアイコン）を使い、IME フリック入力を避ける」と書いておくとチームへの周知にもなります。

```markdown
## iOS Remote Control からのコメント送信について
日本語は IME フリック入力の最初の文字が壊れるバグ中（Issue #83283）。
回避: フォーカス後に空欄 Backspace → タップし直さず即入力。
または音声入力を活用。
```

修正には React Native TextInput の `value` 再代入タイミングの調整が必要で、本体の修正待ちになります。
