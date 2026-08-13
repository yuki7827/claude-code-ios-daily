---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-08-14"
date: 2026-08-14 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- **v2.1.229（2026-08-12）リリース**（[Claude Code 変更ログ](https://code.claude.com/docs/en/changelog)）— iPhone から Remote Control を使う開発者に特に効く変更が多数含まれた大型修正リリース。iOS 開発観点での主な変更: ① **`claude remote-control --continue` を正式ドキュメント化** — 引数なしで直前の Remote Control セッションを即座に再開できるコマンド。XcodeBuildMCP のビルドセッションを Mac 側ターミナルで起動し、iPhone の Code タブに切り替えても後からこのコマンド 1 つで元のセッションに再接続できる; ② **SSE キープアライブ Ping を追加** — 長い思考フェーズ（XcodeBuildMCP の archive ビルド・UI テスト等）中に Vertex / Bedrock アップストリームがアイドルタイムアウトで接続を切るケースを防止; ③ **`ListAgents` が切断済み Remote Control セッションを `offline` として表示** — iPhone 側から Mac の Remote Control セッション一覧を確認したとき、切断中のセッションが明示されるため混乱が減少; ④ **Remote Control クライアントがスラッシュコマンド後にスピナーが固まるバグを修正** — Mac ターミナルで `/model` 等を実行した後 iPhone 側の操作が詰まる問題が解消; ⑤ **プラグインマーケットプレース `command` ソースを追加** — ローカルコマンド（Xcode 等）が返したパスをプラグインディレクトリとして採用でき、セッション再起動不要でプラグインを切り替え可能に。

- **v2.1.231（2026-08-13）リリース**（[Claude Code 変更ログ](https://code.claude.com/docs/en/changelog)）— **事前登録済み OAuth クライアント（Slack 等）を使う MCP サーバーでの OAuth サインインが、リダイレクト URI の不一致で失敗する問題を修正**。XcodeBuildMCP と併用するチームが CI 通知や Slack Bot MCP を Claude Code から呼び出している場合に直結するバグ。内部的には `localhost` ではなく `127.0.0.1` を使うことで strict な認証サーバーとの互換性を回復した。

## 🛠 GitHub の動き

- [Issue #83011 — iOS Simulator ヘルパー（claude-ios-sim）が macOS 27 beta で crash-loop — Metal の `recordBinaryArchiveUsage` 内の uncaught NSException（OPEN / 2026-07-31）](https://github.com/anthropics/claude-code/issues/83011) — 既報 #80472（サンドボックスプロファイルが Metal シェーダーキャッシュディレクトリ作成を拒否）と根本は同じだが、本 Issue はクラッシュ連鎖の「もう一段深い原因」と再現条件を詳細に分析した独立した報告。**クラッシュシグネチャが `CoreImage CIContext init → Metal recordBinaryArchiveUsage → NSArray initWithObjects:count: に nil が渡る → uncaught NSInvalidArgumentException` と完全に特定されており、ユーザーが再現できる最小コード（`CIContext(mtlDevice:)` を呼ぶだけの 7 行 Objective-C）と、ヘルパーが graceful degradation（CoreImage 失敗時にソフトウェアレンダラーへフォールバックする、または MCP ツール結果に "OS beta 非互換" エラーを明示する）すべきという提案が含まれる。** また、`attach` 時に引数なしだと「No booted simulator found」と返すが、明示的 UDID を指定すれば panel は開く（その後数秒でクラッシュ）という挙動差も記録されており、デバイス検出ロジックの別バグの可能性を指摘している。

## 📝 日本語コミュニティ

- [Claude Code 標準 Skill を iOS アプリ開発でどう使うか：公式ドキュメントを根拠に整理する（Qiita / 4q_sano）](https://qiita.com/4q_sano/items/0ecf0fc7353d751f601c) — `dataviz`・`code-review`・`simplify` などの Claude Code 標準 Skill を iOS プロジェクトで有効活用する方法を、公式ドキュメントの記述を根拠として整理した記事。XcodeBuildMCP との組み合わせで「ビルドを通す → /code-review で品質チェック → /simplify でリファクタ」という連続フローを組む具体的な手順が示されており、スキルの効果的な使い分けを理解するのに有用。直近 5 投稿での掲載なし。

- [Xcode 26.3 エージェント型コーディング入門 — Claude・Codex・MCP で開発を自動化（Qiita / kai_kou）](https://qiita.com/kai_kou/items/1b24ad62dde4c02ae4f0) — Xcode 26.3 に追加されたエージェント型コーディング機能（Claude Agent SDK・Codex・MCP）の全体像を初心者向けに解説した入門記事。`xcrun mcpbridge` のセットアップから SwiftUI 画面の自動生成・ビルド検証までを一通り体験できる構成で、Claude Code を Xcode と組み合わせて使い始める際の手順書として活用できる。直近 5 投稿での掲載なし。

## 🌐 海外コミュニティ / Tips

- [XcodeBuildMCP: Build & Test iOS Apps from Claude (2026)（mcp.directory）](https://mcp.directory/blog/xcodebuildmcp-complete-guide-2026) — Sentry が管理する XcodeBuildMCP の 2026 年版完全ガイド。v2.3.2 時点での 82 ツール（シミュレータ・デバイス・デバッグ・プロジェクト内省・UI 自動化）の全体像と Claude Code での設定方法が網羅されている。「AI エージェントがコンパイルエラーを自律修正しながらビルドを繰り返すループ」を iOS 開発で実現するためのエントリポイントとして参照しやすい内容。直近 5 投稿での掲載なし。

## 💡 今日のおすすめ実践 Tip

**「`claude remote-control --continue` でモバイルワークフローを効率化する — Mac ↔ iPhone のセッション切り替えを即座に再開」**

v2.1.229 で正式ドキュメント化された `claude remote-control --continue` は、**引数なしで直前の Remote Control セッションを再開する**コマンドです。XcodeBuildMCP を使った iOS 開発では「Mac のターミナルでビルドを仕掛けた後、iPhone の Code タブに切り替えて進捗を確認する」というパターンが多いですが、セッション ID を探す手間が毎回発生していました。このコマンドでその手間がなくなります。

**典型的な活用パターン**

```bash
# Mac 側: Remote Control デーモンを起動してビルドを走らせる
claude --remote-control
# Claude Code が XcodeBuildMCP でビルドを開始...

# しばらく後: Mac のターミナルでセッションが切れている場合
claude remote-control --continue
# → 直前のセッションに即座に再接続。会話履歴もそのまま継続
```

**iPhone の Code タブとの組み合わせ**

iPhone の Code タブからセッションに接続した後でも、Mac 側で `claude remote-control --continue` を実行すれば**同じセッションを Mac のターミナルからも継続操作**できます。複雑なコマンドを打ちたいときはデスクトップに戻り、確認・承認だけ iPhone でする、という使い分けが自然になります。

**v2.1.229 の SSE キープアライブと組み合わせた長時間ビルド運用例**

```
iPhone Code タブで Remote Control に接続
  ↓
Claude Code に fastlane beta（archive → upload）を指示
  ↓
[SSE keepalive ping が定期的に送信され、アイドルタイムアウトを防止]
  ↓
アップロード完了後に iPhone で結果を確認
  ↓
何か追加指示が必要なら Mac に戻って claude remote-control --continue
```

`--continue` は `--resume <session_id>` の省略形として機能します。セッション ID を `.claude/` 以下から探す必要がなくなるため、特に**スケジュール実行ルーティン後の確認作業**でも手軽に使えます。

**`ListAgents` の `offline` 表示との連携**

v2.1.229 では `ListAgents` ツールが切断済みセッションを `offline` と明示するようになりました。Claude Code のセッション内から `ListAgents` を呼び、`offline` と表示されているセッションがあれば `claude remote-control --continue` でそこに再接続する、という自動化も可能です。
