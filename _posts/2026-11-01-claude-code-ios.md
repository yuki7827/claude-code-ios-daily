---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-11-01"
date: 2026-11-01 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [v2.1.316（2026-10-31）](https://code.claude.com/docs/en/changelog) — 本日未明のリリース。iOS 開発に関連する主な変更点: ① `CLAUDE_CODE_DISABLE_ULTRATHINK` 環境変数を追加（`ultrathink` キーワードを組織全体で無効化できる。CI / 自動化スクリプトで誤って xhigh effort が走るのを防ぐ用途に有効）。② `CLAUDE_CODE_SUBAGENT_EFFORT` を追加（マルチエージェント構成でサブエージェントの effort レベルをまとめて設定でき、iOS 向けサブエージェント開発でのトークン消費を管理しやすくなる）。③ Opus 4.8 の fast mode を **非推奨化**（2026-11-30 削除予定、Opus 5.5 への移行推奨）。34 件のバグ修正。

- [v2.1.314（2026-10-30）](https://code.claude.com/docs/en/changelog) — iOS 開発に直結する重要変更: ① `permissions.blockWritesOutsideWorkingDirectories` を追加（全 permission mode でワーキングディレクトリ外への Write / Edit / NotebookEdit を拒否。Xcode プロジェクトルートを working directory に設定することで、Claude が `.pbxproj` / `.xcconfig` / `Podfile.lock` などプロジェクト外ファイルを誤って書き換えるリスクをゼロにできる）。② VS Code 拡張と Claude Desktop の Code タブで **idle compaction** を導入（20 万トークン超のセッションが一定時間アイドルになると自動要約。長時間のビルドログ・テスト結果を大量に読み込んだ後でも context を節約できる）。81 件のバグ修正。

## 🛠 GitHub の動き

- 該当なし（2026-11-01 時点で iOS / Xcode 関連の新規 Issue は確認できず。直近の主要 Issue は 10-31 キャッチアップを参照）

## 📝 日本語コミュニティ

- 該当なし（2026-11-01 時点で Zenn / Qiita に新規の Claude Code × iOS 記事は確認できず）

## 🌐 海外コミュニティ / Tips

- 該当なし（本日時点で新規の注目記事は確認できず）

## 💡 今日のおすすめ実践 Tip

**`permissions.blockWritesOutsideWorkingDirectories` で Xcode プロジェクト外への誤書き込みをゼロにする**

v2.1.314 で追加された `permissions.blockWritesOutsideWorkingDirectories` は、iOS 開発者にとって待望の設定だ。Claude Code は Xcode プロジェクトに含まれる `.pbxproj`・`.xcconfig`・`Podfile.lock`・`Package.resolved` などを正しい場所に置く必要があるが、working directory の設定が曖昧だとプロジェクト外を書き換えてしまうケースがある。

プロジェクトの `.claude/settings.json` に下記を追記するだけで有効になる:

```json
{
  "permissions": {
    "blockWritesOutsideWorkingDirectories": true
  }
}
```

あわせて Claude Code を常に **Xcode プロジェクトルート**（`.xcodeproj` または `.xcworkspace` が置かれているディレクトリ）から起動する習慣をつけると、この設定が最も効果的に機能する。`~/` や `/tmp` など意図しない場所への出力ファイル生成も防げるため、CI 上での Claude Code 実行でも安心して使えるようになる。
