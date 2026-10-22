---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-10-23"
date: 2026-10-23 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [v2.1.308（2026-10-22）](https://code.claude.com/docs/en/changelog) — バグ修正と安定性改善のみ。詳細は非公開。
- [v2.1.307（2026-10-21）— `claude_code.safety_event` OTel イベント追加・`@claude review stop` を Code Review に追加](https://code.claude.com/docs/en/changelog) — permission rule・auto mode・safety check・hook・sandbox がツール呼び出しを停止・プロンプト・返却したタイミングで `claude_code.safety_event` が発火するようになった。CI ログや OTel バックエンドに安全判断の根拠を記録できる。また、PR への自動コードレビューを `@claude review stop` コメントで個別にオプトアウトできるようになった。iOS CI パイプラインで特定の PR だけレビューを止めたい場合に便利。

## 🛠 GitHub の動き

- 該当なし（2026-10-23 時点で anthropics/claude-code への REST API アクセスが制限されており、iOS / Xcode / Swift 関連の新規 Issue を確認できず）

## 📝 日本語コミュニティ

- [Claude Code に iOS シミュレータの動作確認まで自走させる — FlowDeck と AXe で作る自動 UI 検証（Qiita / suzuhiroruri）](https://qiita.com/suzuhiroruri@github/items/5a1560b36e9ee8411f5b) — 「ビルドは通りました。手元で確認してください」で終わりがちな Claude Code を、FlowDeck と AXe を組み合わせてシミュレータの UI 検証まで自走させる構成を解説した記事（2026-10-21 投稿）。複数セッションでシミュレータやキャッシュが競合するケースへの対策も紹介されている。

## 🌐 海外コミュニティ / Tips

- [artemnovichkov/xcode-mods — Xcode のビルド・テスト・コンソール・SwiftUI Preview を Claude Code の中で表示する Mod](https://github.com/artemnovichkov/xcode-mods) — Claude Code の Mods 機能（v2.1.287〜）を使い、ツールバーバンドに現在のスキーム・エラー数・テスト失敗数を表示しつつ、「Xcode ペイン」でビルド結果とテスト一覧を、「Canvas ペイン」で SwiftUI `#Preview` のスナップショットをリアルタイムに確認できる。`/build`・`/tests`・`/run`・`/preview` などのスラッシュコマンドも提供。Xcode 27 以降・Claude Code 2.1.286 以降が必要。

## 💡 今日のおすすめ実践 Tip

**`@claude review stop` で特定 PR の自動レビューをオプトアウトする**

v2.1.307 で追加された `@claude review stop` を使うと、自動コードレビューが有効なリポでも特定の PR だけをオプトアウトできる。iOS プロジェクトでよくある「WIP の UI 実装」「リリースブランチへのマージ準備 PR」など、Claude に自動レビューさせると余計なコメントが増える PR で活用できる。

**使い方:**

1. PR のコメントに `@claude review stop` と書き込む
2. 以降、その PR へのプッシュに対して自動レビューが走らなくなる
3. 再開したい場合は `@claude review` コメントで再トリガー可能

**iOS チームでの使いどころ:**

- `WIP:` や `DO NOT MERGE` ラベルを付けた作業中 PR（`@claude review stop` で静かに保てる）
- リリースブランチへのバージョンバンプ PR（コード変更がなく自動レビューは不要）
- デザイナーとの UI キャプチャ比較など、コードレビューより目視確認が主目的の PR

**注意点:**
- `@claude review stop` が使えるのは GitHub App 経由のコードレビュー機能のみ。ローカルの `/code-review` スラッシュコマンドには影響しない
- 権限の設定によっては、コメントを投稿できるロールが制限されている場合があるため、チームの GitHub App 設定を確認すること
