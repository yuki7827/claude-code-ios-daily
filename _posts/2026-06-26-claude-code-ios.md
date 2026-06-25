---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-06-26"
date: 2026-06-26 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Claude Code v2.1.191（2026-06-24）](https://code.claude.com/docs/en/changelog) — `/rewind` コマンドを追加：`/clear` でコンテキストをリセットする前の状態に会話を巻き戻せる。長時間の `xcodebuild` デバッグセッションで「誤って `/clear` してしまった」際のロールバック手段として有用。またテキスト更新の合算間隔を 100ms に変更することでストリーミング中の CPU 使用率を約 37% 削減。`xcodebuild` と Claude Code を Mac 上で並行稼働させる iOS CI 環境でサーマルスロットリングが発生しにくくなる。

## 🛠 GitHub の動き

- [CarolaneLFBV/ios-development-agents](https://github.com/CarolaneLFBV/ios-development-agents) — iOS 開発に特化した Claude Code エージェントフレームワーク。15 のコマンドと 7 つの Opus エージェント（SwiftUI スペシャリスト・アーキテクト・パフォーマンス・テスト・セキュリティ・DevOps）を収録し、74% のコンテキスト削減を実現。Swift 6.2・SwiftUI・SwiftData に対応し、フレームワーク本体はわずか 3 ファイル（約 316 行）の軽量設計。

- [johnrogers/claude-swift-engineering](https://github.com/johnrogers/claude-swift-engineering) — Swift / TCA（The Composable Architecture）に特化した Claude Code エージェント・スキル集。12 の専門エージェントと 18 のナレッジスキルを収録し、Swift 6.2 の strict concurrency・async/await・SwiftUI の最新プラクティスに対応。TCA を採用した iOS プロジェクトで設計・実装・ドキュメント・テストを Claude に委譲する際の参照ガイドとして活用できる。

- [rohitg00/awesome-claude-code-toolkit](https://github.com/rohitg00/awesome-claude-code-toolkit) — Claude Code のプラグイン・エージェント・スキルを網羅したキュレーションリポジトリ。176+ プラグイン・135 エージェント・35 スキル・42 コマンドを収録し、うち 16 エージェントが Swift 専用（SwiftUI・Swift Concurrency・Core ML・Foundation Models など）。iOS 開発に使えるリソースの全体像を把握したい場合の出発点として有用。

## 📝 日本語コミュニティ

- 該当なし（本日時点で新規確認できた iOS × Claude Code の日本語記事なし）

## 🌐 海外コミュニティ / Tips

- [jamesrochabrun/ClaudeCodeSDK](https://github.com/jamesrochabrun/ClaudeCodeSDK) — AgentHub・xclaude-plugin の作者 jamesrochabrun による、Claude Code を Swift アプリに統合するための Swift パッケージ。デュアルバックエンド対応・マルチターン会話・JSON/ストリーミングレスポンス・MCP 統合をサポート（macOS 13+ / Swift 6.0+）。Claude Code CLI の ClaudeCodeSDK 上に独自の macOS ツールを構築する際の参照実装として活用できる。

## 💡 今日のおすすめ実践 Tip

**「`/rewind` で誤った `/clear` から即復帰 — iOS デバッグセッションを無駄にしない」**

コード署名エラーやプロビジョニングプロファイルの不一致など、iOS ビルド固有の複雑なエラーを Claude と一緒に追いかけているセッションで `/clear` を誤って実行すると、試行錯誤の文脈がすべて消えてしまっていた。v2.1.191 の `/rewind` はその問題を解消します。

```
# XcodeBuildMCP でビルドエラーと格闘中に誤って /clear してしまった場合
/rewind

# → /clear 前の状態に会話が復元される
# → ビルドログ・エラー内容・試みた修正の文脈がそのまま残る
# → 「どこまで確認済みか」を Claude が把握した状態でデバッグを再開できる
```

加えて、CPU 使用率の 37% 削減も iOS 開発では実質的な恩恵があります。`xcodebuild`（大規模プロジェクトのフルビルドで CPU 100% 近くを消費）と Claude Code を同時に動かすと Mac がサーマルスロットリングに入りやすかったが、アイドル時の Claude のオーバーヘッドが減ることでビルドに割り当てられるリソースが増えます。特に M1/M2 MacBook Pro で iOS ビルド＋テストを Claude Code に自律実行させる構成では、バッテリー持ちとスロットリング頻度の改善が期待できます。
