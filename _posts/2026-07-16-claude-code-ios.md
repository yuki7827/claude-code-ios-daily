---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-07-16"
date: 2026-07-16 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Claude Code v2.1.210（2026-07-14）](https://code.claude.com/docs/en/changelog) — ツール呼び出しの折り畳みサマリー行にリアルタイム経過時間カウンターを追加（停止しているように見える問題を解消）。長い段落のストリーミングが 1 行ずつ表示されるようになり、テキスト更新の集約により**ストリーミング時の CPU オーバーヘッドが約 37% 削減**。iOS 開発での直接的な効果として、XcodeBuildMCP 経由の長いビルドログ取得中の体感レスポンスが向上する。Remote Control 関連の重要修正も複数含む：①タスクステータスが "Running" のまま固まらなくなった、②別アカウントにサインインするとセッションが切断される問題の修正、③接続失敗時の "/rc failed" インジケーターを画面に残留させてユーザーが気づきやすくした。さらに overnight retire 後にバックグラウンドセッションが会話履歴を失い再実行してしまうバグ、および `claude attach` で "job not found" / "agent is still starting" が出ていた問題も修正。

## 🛠 GitHub の動き

- [Issue #77871 — Remote Control: デスクトップアプリの自動アップデート後にセッションが再接続できず iPhone からの許可操作が届かなくなる（OPEN / 2026-07-15 新規）](https://github.com/anthropics/claude-code/issues/77871) — 約 23 時間稼働していた iOS ビルドセッションが、Claude Desktop の自動アップデートをきっかけに iPhone の Remote Control からは "Disconnected" 表示になり切断。アップデート後に新規作成したセッションは正常に繋がるが、アップデート前のセッションは永続的に再接続不能。最悪のケースとして、切断されたセッションが Bash ツールの許可待ちになりデスクトップ上でフリーズしたまま iPhone から操作できなくなる。暫定回避策：別セッションのセッション管理 MCP ツール（`archive_session`）でフリーズ中のセッションを停止してから再開。v2.1.210 の Remote Control 修正で部分的に改善されている可能性あり。

- [Issue #34255 — Remote Control: 自動再接続が機能せず接続がサイレントにドロップする（OPEN・👍97 件・2026-07-15 更新）](https://github.com/anthropics/claude-code/issues/34255) — Remote Control セッションが 15〜60 分後にサイレントドロップし、iOS アプリ側もターミナル側も通知なしで完全に操作不能になるバグ。手動では TUI メニューで "Disconnect" → 再起動で必ず復旧するが、自動再接続は一度も成功しない。XcodeBuildMCP で長時間ビルド・テストを走らせている場合に特に問題で、外出中は物理的に Mac を操作できないためセッションが永久に失われる。2026-07-15 に再度コメントが増加しており、v2.1.210 の RC 修正で改善が期待されている。

- [Issue #25664 — SSH リモートがローカルの swift-lsp などプラグインパスを Linux サーバーに送り込みハングする（OPEN / 2026-07-15 更新）](https://github.com/anthropics/claude-code/issues/25664) — Mac で Claude Code を SSH リモートに接続すると、ローカルの `~/.claude/plugins/cache/.../swift-lsp/1.0.0` などのプラグインパスや MCP 設定をそのまま Linux サーバーへ渡してしまい、ccd-cli が存在しないパスで起動しようとして約 280MB のメモリを消費したまま無限ハング。「iOS プロジェクトを Mac で開発しつつ CI ビルドサーバー（Linux）に SSH で接続して Claude Code を動かす」ワークフローで必ず踏む問題。回避策：`claude --no-plugins` フラグ（SSH 接続時のみ）でローカルプラグインを除外する。

## 📝 日本語コミュニティ

- [コードを1行も書かずに25,000行のiOSアプリを作った話 ― Claude Codeで完全Vibe Coding（Qiita / john-rocky）](https://qiita.com/john-rocky/items/b5a24858e797c954b16f) — 11 日間（2026/2/15〜2/26）、136 コミットすべてを Claude Code に生成させ、SwiftUI + SwiftData + FoundationModels + AVFoundation 構成の iOS アプリ（155 ファイル・25,000 行超）を完成させた事例。人間はプロンプトと最終レビューのみを担当。「Claude Code は Swift のエコシステムの知識が豊富で、FoundationModels（Apple Intelligence のオンデバイスモデル）を含む最新フレームワークも問題なく扱えた」と報告。大規模 SwiftUI アプリの Vibe Coding ポテンシャルを示す実践事例。

- [Claude Codeにプロダクションコードを全チェックさせたら、金銭バグ4件を含む15件の不具合を自動修正してくれた話（Qiita / sorabcjanne1）](https://qiita.com/sorabcjanne1/items/8f3a354ff073ed69aac2) — 既存の Swift 本番コードをリポジトリごと Claude Code に読み込ませ、「バグがないか全チェックして修正して」と指示したところ、金銭計算の誤りを含む 15 件の不具合が自動修正された体験談。人間がテストや CI で見落としていた型変換ミスや端数処理バグが検出されており、「コードレビューの前段として Claude Code に通すだけで品質が明らかに上がった」と結論。Swift での金銭計算（`Decimal` 型の扱い等）に関して Claude Code が精度を発揮している点に注目。

## 🌐 海外コミュニティ / Tips

- [CharlesWiltgen/Axiom — 264 skills・41 agents の iOS 特化 Claude Code プラグイン（Claude Code CLI で一発インストール）](https://github.com/CharlesWiltgen/Axiom) — Apple OS（iOS / iPadOS / watchOS / tvOS）開発向けに特化した Claude Code スキル集。264 skills と 41 の自律エージェント（auditor）を搭載し、質問内容から自動的に適切なスキルが起動する。主要コマンド：`/axiom:console`（シミュレータ出力キャプチャ）、`/axiom:analyze-crash`（クラッシュレポート解析）、`/axiom:fix-build`（ビルドエラー診断）、`/axiom:audit memory`（メモリリーク検出）、`/axiom:audit concurrency`（データレース検出）。インストールは `/plugin marketplace add CharlesWiltgen/Axiom` の 1 コマンド。iOS 27 beta 対応も進行中。

- [ApptitudeLabs/claude-code-ios-dev-setup — Axiom + RTK + Caveman + XcodeBuildMCP を組み合わせた本番品質 iOS 開発セットアップガイド](https://github.com/ApptitudeLabs/claude-code-ios-dev-setup) — PRD 駆動開発・Axiom スキル・XcodeBuildMCP・拡張思考（ultrathink）・トークン最適化ツール（RTK でコンテキスト 60〜90% 削減、Caveen で出力トークン約 75% 削減、Memvid でナレッジベース圧縮）を統合したセットアップガイド。Anthropic ハッカソン優勝経験を持つ著者が 10 ヶ月の実運用から蒸留した構成で、3 つの Xcode MCP オプション（XcodeBuildMCP・mcpbridge・xc-mcp）の用途別使い分けも解説している。

## 💡 今日のおすすめ実践 Tip

**「Axiom プラグインで iOS 開発の品質チェックを自動化する」**

`CharlesWiltgen/Axiom` はインストール後に質問内容から自動でスキルが起動するため、追加設定なしに使い始めることができます。

**1. インストール（Claude Code セッション内）**

```
/plugin marketplace add CharlesWiltgen/Axiom
```

**2. 主要コマンドの実際の使い方**

```
# ビルドが通らないとき（ビルドログを読み込み自律診断）
/axiom:fix-build

# シミュレータのコンソールログをキャプチャしてデバッグ
/axiom:console

# クラッシュレポート（.ips ファイル）を貼り付けて解析
/axiom:analyze-crash

# メモリリーク・循環参照を自動検出
/axiom:audit memory

# Swift Concurrency の data race を検出
/axiom:audit concurrency
```

**3. 「金銭バグ防止」に応用する**

今日紹介した Qiita 記事（sorabcjanne1 氏）の体験を参考に、リリース前に以下のようなプロンプトと Axiom を組み合わせると、人間レビューで見落としがちな計算バグを事前に拾えます：

```
このリポジトリのコード全体を読んで、以下を確認してください：
1. 金銭計算で Float/Double を使っている箇所（Decimal 型に変更が必要）
2. 端数処理（切り捨て/切り上げ/四捨五入）の一貫性
3. 通貨フォーマット（NumberFormatter の設定ミス）

問題があれば自動修正してください。
```

**4. Remote Control 接続問題への対策（v2.1.210 以降）**

今日取り上げた Issue #77871 / #34255 の対策として、長時間ビルドをバックグラウンドで走らせる際は Bark 通知（zenn.dev/schroneko、2026-07-15 で紹介済み）と組み合わせ、セッション切断を即座に検知できる体制を整えておくことを推奨。v2.1.210 でも完全に修正されていない可能性があるため、重要なビルドセッションは定期的に iPhone から疎通確認する習慣をつけましょう。
