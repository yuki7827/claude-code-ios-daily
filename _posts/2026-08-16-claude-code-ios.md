---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-08-16"
date: 2026-08-16 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- **v2.1.233（2026-08-14）リリース**（[Claude Code 変更ログ](https://code.claude.com/docs/en/changelog)）— iOS 開発観点での主な変更をまとめる。① **MCP v2 接続がストリームタイムアウトで無限に subscriptions/listen ストリームを再接続し続ける問題を修正** — XcodeBuildMCP との長時間ビルドセッション（archive ビルド・実機テスト等）中にサーバー側がタイムアウトで接続を切ると、クライアントが再接続ループに入って応答しなくなるケースが解消された。archive ビルドのように10〜30分かかる処理を Claude Code に任せている開発者には直結する修正; ② **Remote Control: 削除済みセッションの `resume` が代替セッションを起動するよう変更** — claude.ai または アプリ側で古いセッションを削除してしまった場合に `--continue` がエラーで失敗していた問題を修正。iPhone の Code タブから再接続を試みても「セッションが見つからない」と返されるケースが解消; ③ **クラウドセッションがパーミッションプロンプト待機中に「lost」とマークされる問題を修正** — XcodeBuildMCP のビルド中に突発的なパーミッションプロンプトが発生し、ユーザーが応答する前にセッションが「lost」扱いになってビルドコンテキストが消えるケースが解消; ④ **mTLS クライアント証明書のローテーションが再起動なしで自動適用されるよう変更** — 証明書を更新しても Claude Code の再起動が不要になる（企業内 mTLS プロキシ経由で Claude Code を利用する場合に有効）。

## 🛠 GitHub の動き

- **iOS Simulator Metal サンドボックスクラッシュ関連 Issue が 2026-08-15 に一斉クローズ**（[Issue #80472](https://github.com/anthropics/claude-code/issues/80472) ほか）— 昨日（2026-08-15）の 16:09〜17:11 UTC にかけて、macOS 27 beta で `claude-ios-sim` ヘルパーが SIGABRT でクラッシュし続ける問題を報告していた Issue 群（#80177・#80472・#82864・#83011・#84531・#84943・#86757）が一斉にクローズされた。これらの Issue はいずれも同一の根本原因（`claude-ios-sim.sb` サンドボックスプロファイルが Metal シェーダーキャッシュディレクトリ作成を拒否 → `MTLGetShaderCachePath()` が nil を返す → `-[_MTLDevice recordBinaryArchiveUsage:]` が nil を NSArray に詰めて NSInvalidArgumentException で SIGABRT）を共有しており、最も詳細な根本原因分析は 2026-08-14 付けの [Issue #86757](https://github.com/anthropics/claude-code/issues/86757)（本記事で昨日「OPEN」として紹介したばかりのもの）で提供されている。Issue 群が同一時間帯に一斉クローズされたことは、**Anthropic が Claude Desktop アップデートで `claude-ios-sim.sb` のサンドボックスプロファイル修正を含む修正をリリースした**ことを強く示唆する（正式なリリースノートは Claude Desktop の変更ログで要確認）。macOS 27 beta + XcodeBuildMCP でなんとか乗り切っていた開発者は、Claude Desktop を最新版に更新することで組み込みの iOS Simulator パネルが利用可能になっているか確認する価値がある。

## 📝 日本語コミュニティ

- 該当なし

## 🌐 海外コミュニティ / Tips

- [awesome-agent-skills（VoltAgent / 30.3k ⭐）](https://github.com/VoltAgent/awesome-agent-skills) — 公式チームとコミュニティから手動でキュレーションした 1,400+ のエージェントスキルをまとめたリポジトリ（「AI 生成ではなく手動選定」を明示）。iOS 開発に直接使えるスキルとして、**Sentry Cocoa SDK スキル**（iOS・macOS・tvOS・watchOS・visionOS 全対応の Sentry エラー監視設定）、**XCUITest スキル**（Swift で iOS/iPadOS UI テストコードを自動生成）、**Expo UI SwiftUI スキル**（SwiftUI コンポーネントを Expo プロジェクトで活用）が収録されている。ほかにも XcodeBuildMCP・Axiom・conorluddy/ios-simulator-skill・twostraws/SwiftUI-Agent-Skill など iOS 開発者が使うべきスキル群がまとめられており、「まず何のスキルを入れればよいか」を調べる際の出発点として有用。全投稿での初掲載。

## 💡 今日のおすすめ実践 Tip

**「v2.1.233 の MCP v2 タイムアウト修正 — XcodeBuildMCP を使った長時間ビルドの安定化」**

v2.1.233 で修正された MCP v2 接続の無限再接続バグは、iOS 開発者にとって特に impact が大きい修正です。XcodeBuildMCP の `archive` ビルドや実機テスト実行は10〜30分かかることがあり、その間に MCP サーバー側がタイムアウトで接続を切ると、以前はクライアントが応答しなくなっていました。

**具体的な症状（修正前）**

```
# 以下のようなログが繰り返し出ていた場合は、このバグが原因
[MCP] subscriptions/listen stream terminated (server timeout)
[MCP] Reopening subscriptions/listen stream...
[MCP] subscriptions/listen stream terminated (server timeout)
... （無限ループ）
```

ツール呼び出しが返ってこなくなり、セッションを強制終了するしかなかったケースが解消されています。

**推奨アクション**

```bash
# 現在のバージョン確認
claude --version

# v2.1.233 未満の場合は更新
npm update -g @anthropic-ai/claude-code
# または
claude update
```

v2.1.233 以上であれば修正済みです。なお、XcodeBuildMCP を使った長時間ビルドで「なぜか途中で止まる」という問題を経験していた方は、このバグが原因だった可能性が高いため、更新後に再試行してみてください。

**合わせて確認したい v2.1.233 の変更**

Remote Control のセッション削除後の挙動変更も iOS 開発ワークフローに直結します。iPhone の Code タブからリモートでビルドを指示した後、Mac 側でセッションを誤って閉じてしまっても、`--continue` で代替セッションが起動するようになったため、ビルドコンテキストの再構築コストが下がりました。

```bash
# セッションが削除されていても代替セッションが起動される（v2.1.233 以降）
claude remote-control --continue
# → 以前は「Session not found」エラーで失敗していた
# → 今は新しいセッションが起動してワークフローを継続できる
```
