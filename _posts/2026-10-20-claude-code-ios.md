---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-10-20"
date: 2026-10-20 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- **[v2.1.303（2026-10-18）リリース](https://code.claude.com/docs/en/changelog)** — バグ修正と安定性改善のみ。Xcode ワークツリーまわりの複数 fix が含まれており（v2.1.300 に引き続き）、`git worktree` でブランチごとに Xcode プロジェクトを分離している iOS 開発者には恩恵がある可能性がある。

- **[v2.1.302（2026-10-16）— `CLAUDE_CODE_AUTO_MODE_MODEL` 環境変数を追加、フォーク型サブエージェントをデフォルト on に拡大](https://code.claude.com/docs/en/changelog)** — Auto モード時に使用するモデルを環境変数で固定できるようになった。`.env` や Shell プロファイルに `CLAUDE_CODE_AUTO_MODE_MODEL=claude-haiku-5-5` と書けば、Auto モードが常に Haiku 5.5 を選ぶ設定が可能（コスト制御に有用）。フォーク型サブエージェント（コンテキストを引き継いだ並列処理）がより多くの環境でデフォルト有効化された。

- **[v2.1.301（2026-10-15）— vim `%` モーションの追加、OpenTelemetry イベントに `classifier_verdict` と `permission_mode` を追加](https://code.claude.com/docs/en/changelog)** — CLI で vim モードを使っている開発者向けに `%` での対応括弧ジャンプが使えるようになった。OTel に `classifier_verdict`（判断根拠）と `permission_mode`（実行中のパーミッションモード）が含まれるようになり、CI ログやモニタリング基盤への組み込みがしやすくなった。

- **[v2.1.295（2026-10-08）— hooks に `onFailure: "block"` を追加](https://code.claude.com/docs/en/changelog)** — PreToolUse フックが失敗したときの挙動を `"block"` に指定できるようになった。swiftlint や swift-format が見つからない、またはタイムアウトした場合にツール呼び出し自体をブロックできる。サイレントな lint スキップを防ぐ用途で iOS プロジェクトに有用。

## 🛠 GitHub の動き

- 該当なし（2026-10-20 時点で anthropics/claude-code における iOS / Xcode / Swift 関連の新規 Issue・PR は確認できず）

## 📝 日本語コミュニティ

- 該当なし（2026-10-20 時点で Zenn・Qiita の新着記事は確認できず）

## 🌐 海外コミュニティ / Tips

- **[Best Swift / iOS Skills for Claude Code (2026)（claudeskills.info）](https://claudeskills.info/best/swift-ios-skills/)** — Claude Code 向けの Swift / iOS スキルを網羅的にまとめたキュレーションページ。`apple-platform-build-tools`（kylehughes 製）・XcodeBuildMCP CLI スキル・hmohamed01 の swift-development スキルなど代表的なものを一覧化。各スキルの対象（ビルド・テスト・コード生成・デバッグ等）を比較形式で整理しており、複数スキルを組み合わせる際の選択指針として参考になる。

- **[10 tips to maximize Claude Code for iOS development（Medium / CodeToDeploy）](https://medium.com/codetodeploy/10-tips-to-maximize-claude-code-for-ios-development-29027edc51a7)** — 実務ベースで厳選された 10 の Tips。「Swift / UIKit / SwiftUI の理解なしでは AI に正しい指示を出せない」という前提のもと、CLAUDE.md への詳細なアーキテクチャ記述、`@`ファイルメンションを使った部分コンテキスト渡し（大型 `.swift` の一部だけ渡す）、test-first アプローチで Claude に仕様を先に書かせてからコードを実装させる手順などを解説。

## 💡 今日のおすすめ実践 Tip

**`CLAUDE_CODE_AUTO_MODE_MODEL` で iOS ビルド自動化コストを制御する**

v2.1.302 で追加された `CLAUDE_CODE_AUTO_MODE_MODEL` を使うと、Auto モード（モデルが自動選択されるモード）で使われるモデルをプロジェクト単位で固定できる。

**基本設定（プロジェクトルートの `.env` または `.envrc`）:**

```bash
# ビルドエラー修正・テスト修正など繰り返し作業は Haiku 5.5 で十分
CLAUDE_CODE_AUTO_MODE_MODEL=claude-haiku-5-5

# 設計・アーキテクチャ検討フェーズは Sonnet/Opus に切り替えたいとき
# CLAUDE_CODE_AUTO_MODE_MODEL=claude-sonnet-5-5
```

**iOS プロジェクトでの使い方:**

```bash
# CI / pre-commit フック専用の設定ファイルで Haiku を固定して実行コストを下げる
CLAUDE_CODE_AUTO_MODE_MODEL=claude-haiku-5-5 \
  claude --auto "xcodebuild test -scheme MyApp ... | tail -30 を読んでテスト失敗を修正してください"
```

**注意点:**
- `CLAUDE_CODE_AUTO_MODE_MODEL` は Auto モード時のモデルを上書きするため、`--model` フラグと併用すると `--model` が優先される
- Haiku 5.5 を固定する場合、100K トークン超えで 5 倍コストになるため、長大な xcodebuild ログは `tail` や `grep error:` で絞ってから渡すこと
- チームで共有する `.env` にこの変数を入れると、全員の Auto モードが統一されるため意図せぬ挙動変化が起きにくい。ただし API コスト管理の観点でチームで合意をとること
