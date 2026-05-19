---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-05-20"
date: 2026-05-20 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Claude Code v2.1.144 (May 19)](https://code.claude.com/docs/en/changelog) — `/resume` がバックグラウンドセッションに対応。`claude --bg` や agent view で起動したセッションが interactive セッション一覧に `bg` 表示で並ぶようになり、`/resume` で再接続が容易になった。iOS 開発者に特に影響する修正として、**macOS で Full Disk Access 保護フォルダ（`~/Documents`・`~/Desktop`・`~/Downloads` 等）配下のプロジェクトでバックグラウンドセッションが `exit 1 before init` でクラッシュしていたバグを解消**（v2.1.143 のリグレッション）。多くの iOS 開発者が Xcode プロジェクトを `~/Documents/` 以下に置くため、バックグラウンドビルドセッションが安定して動作しなかった問題が修正された。MCP の paginated `tools/list` レスポンス処理の改善・拡張子と内容が不一致な画像ファイルのハンドリング修正も含む。

## 🛠 GitHub の動き

- [hmohamed01/swift-development](https://github.com/hmohamed01/swift-development) — Claude Code 向け Swift iOS/macOS 開発スキル（Apr 2026 更新）。SwiftUI `@Observable`（iOS 17+）と `ObservableObject`（iOS 13+）、`NavigationStack` 等のナビゲーションパターン、Swift 6 の async/await・Actor・Sendable・Structured Concurrency、TCA（The Composable Architecture）を網羅する参照ドキュメント 8 本とパターン集を収録。ビルド・テスト・フォーマット・シミュレータ管理の 4 本のヘルパースクリプト付きで、`aj-geddes/ios-swift-development` スキルを元に発展させた構成。Xcode 15+（Swift 6 機能は Xcode 16+）が必要。

- [rshankras/claude-code-apple-skills](https://github.com/rshankras/claude-code-apple-skills) — Apple プラットフォーム開発向け 149 スキル（23 カテゴリ）集（358 stars）。iOS 専用 7 スキル・SwiftUI 5 スキル・テスト & TDD 8 スキル・App Store 最適化 7 スキル・Generator 系 52 スキル（認証/ネットワーク/CloudKit Sync/ライブアクティビティ/ペイウォール等の実装テンプレート）を収録し、macOS Tahoe API・AppKit ブリッジ・watchOS・visionOS もカバー。アイデア出しから App Store 申請まで一気通貫のスキル群として設計されている。

## 📝 日本語コミュニティ

- [LINE iOSアプリ開発を高速化するClaude Code基盤の設計思想](https://techblog.lycorp.co.jp/ja/20260119a) (LY Corporation Tech Blog, Jan 19, 2026) — 大規模 iOS アプリへの Claude Code 導入で培われた設計思想。CLAUDE.md は英語約 600 トークン（約 150 行）に絞り込み、スキルを「Passive（読み込み型）」と「Active（コマンド型）」に分類、ログ収集をサブエージェントに分離してメインコンテキストのノイズを削減する構成。XcodeGen の Project Spec を正確に編集させるため `manipulating-project-spec` Skill を専用定義し、依存関係・ビルド設定の変更を Claude Code に安全に委任できるようにしている。大規模チームでの運用知見として参考度が高い。

## 🌐 海外コミュニティ / Tips

- [Code with Claude: Extended London — May 20, 2026](https://claude.com/code-with-claude/london-extended) — 本日開催の Anthropic 開発者向けハンズオンワークショップ（ライブストリームなし）。独立系デベロッパー・アーリーステージ創業者を対象に Applied AI チームが laptops-open 形式のセッションを提供。前日（May 19）の London メインイベントに続く 2 日目で、Claude Code を使ったプロダクト開発の実践的な内容が中心。次回は東京（6 月 5〜6 日）の予定で、iOS 開発者が多い日本コミュニティにとって最も近いイベントになる。

## 💡 今日のおすすめ実践 Tip

**v2.1.144 の `/resume` + `--bg` で Xcode バックグラウンドビルドセッションを「再開可能」にする**

v2.1.144 で `/resume` がバックグラウンドセッションに対応したことで、iOS ビルドの長時間セッションを「起動して放置→後から状況確認→追加指示」するサイクルが一層スムーズになりました。

```bash
# バックグラウンドでビルド・テストセッションを開始
claude --bg "MyApp の Swift Testing テストがすべて GREEN になるまでビルドと修正を繰り返してください"

# しばらく後に /resume で一覧から再接続（bg セッションが interactive 一覧に並ぶ）
claude --resume
```

`~/Documents` 配下に Xcode プロジェクトを置いている場合、v2.1.143 では Full Disk Access 権限があってもバックグラウンドセッションが初期化直後にクラッシュする問題がありましたが、v2.1.144 で修正済みです。`claude agents` ビューでセッション経過時間を確認しながら `/resume` で再接続して追加指示を送る——このループを繰り返すことで、夜間バックグラウンドビルドの結果を翌朝確認して次のタスクを指示する「非同期ビルドループ」が安定して運用できます。
