---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-06-09"
date: 2026-06-09 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Xcode 27 beta（27A5194q）リリース — Anthropic API がビルトイン統合](https://www.macrumors.com/2026/06/08/apple-unveils-xcode-and-models-improvements/) — WWDC 2026 Keynote（6/8）と同日、Apple Developer Releases で配布開始。Xcode 27 はデュアルエンジン AI を採用：(1) リアルタイムコード補完用のオンデバイスモデル、(2) 重い解析向けの **Anthropic / OpenAI API との標準統合**。Xcode 26.3 では `xcrun mcpbridge` を手動セットアップしていたが、Xcode 27 では追加設定なしで Claude が Coding Tools（Fix / Explain / Generate Tests）から呼び出せる。Craig Federighi が「Xcode はアジェンティック・コーディングの最良の場所になる」と宣言。

- [Foundation Models Framework iOS 27 — マルチモーダル対応・カスタムスキル・サーバー実行を追加](https://developer.apple.com/apple-intelligence/foundation-models-adapter/) — iOS 27 向けフレームワークが**画像入力（マルチモーダル）**、カスタムスキル、サーバーサイドモデル実行に対応。iOS 26 向けアダプターは iOS 27 の新ベースモデルと非互換のため、両 OS サポートアプリはアダプターを OS バージョン別に用意する必要あり。Claude Code でビジョン推論コードを書く際のターゲット API が拡充された。

## 🛠 GitHub の動き

- [jamesrochabrun/ClaudeCodeSDK（新規）](https://github.com/jamesrochabrun/ClaudeCodeSDK) — Swift アプリから Claude Code CLI をサブプロセスとして統合するための Swift ネイティブ SDK。`claude -p` の呼び出しを Swift API でラップし、iOS/macOS アプリ内からエージェント操作をプログラム的に呼び出せる。Xcode 27 Coding Tools とは独立したアプローチで、Swift UI アプリに Claude Code を組み込んだデモツールや開発補助ユーティリティを自作したい場合に有用。

- [Core AI が Core ML を置き換え — MCP がシステムレベルで iOS/macOS 全体に対応](https://byteiota.com/apple-core-ai-replaces-core-ml-ios-27/) — iOS 27 で **Core ML が「Core AI」フレームワーク**に置き換えられることが Keynote で確認。LLM・生成モデルに最適化した設計でサードパーティ製モデルウェイト用のプラグイン機構を内蔵。さらに **MCP 互換コネクターが Core AI アーキテクチャに組み込まれ**、Claude Code が管理する MCP サーバーと Apple のシステム AI ルーティングが将来直結できるアーキテクチャになる。

## 📝 日本語コミュニティ

- [Claude Codeで5日でiOSアプリをApp Storeに出した全記録 — 何を任せて何を任せちゃダメか](https://zenn.dev/seeda_yuto/articles/claude-code-ios-app-5days)（Zenn / seeda_yuto）— 5 日間・55 コミット・約 4,000 行の Swift という実績の詳細記録。「何を Claude に任せて何を任せてはいけないか」という観点で整理されており、設計判断は人間が担い、実装・テスト・エラー解消は Claude Code に委ねるバランスが具体的にまとめられている。直近紹介の 1 日完成記事（06-08）と比べ、複数日スプリントでの運用感が把握できる。

- 該当なし（WWDC 2026 Keynote 直後のため、日本語技術記事の本格的な公開は 6/9 以降に集中する見込み）

## 🌐 海外コミュニティ / Tips

- [SiriKit 廃止確定・App Intents 2.0 が唯一の Siri 統合経路に（TechTimes）](https://www.techtimes.com/articles/318005/20260608/wwdc-2026-app-intents-replaces-sirikit-gemini-siri-migration-clock-starts.htm) — **SiriKit が iOS 27 で正式 deprecated**、移行猶予は 2〜3 年。新 Siri はアプリをまたいで App Intents をチェーン実行でき（ストリーミング応答・会話的フォローアップ付き）、entity schema・intent schema・view annotations を整えることで Siri がアプリ機能を自動「発見」できるようになる。Claude Code で既存 SiriKit Intent 定義を App Intents に自動変換するワークフローが WWDC 後の定番になりそう。

- [Apple Just Turned Every App Into an AI Agent — App Intents・Core AI・MCP の三層構造（FourWeekMBA）](https://fourweekmba.com/wwdc-2026-apple-agentic-ai-siri-extensions-builder-pm/) — WWDC 2026 の開発者向け要点を三層アーキテクチャで整理。「App Intents（能力の宣言）→ Core AI（AI ルーティング）→ MCP（外部エージェント接続）」の構造で、Claude Code は MCP 層の外部エージェントとして iOS アプリの機能を App Intents 経由で呼び出す立ち位置になる。

## 💡 今日のおすすめ実践 Tip

**Claude Code で SiriKit → App Intents 2.0 の移行を今日から始める**

SiriKit の deprecated 確定により、Siri 対応機能を持つすべての iOS アプリが App Intents への移行を求められます。移行猶予は 2〜3 年ですが、iOS 27 の新機能（クロスアプリ Intent チェーン・ストリーミング応答）を活用するには早期移行が前提です。Claude Code は既存の `.intentdefinition` ファイルと Intent Handler を読み込んで App Intents 形式に変換できます。

```
以下の SiriKit ファイルを App Intents に移行してください：
- Intents/OrderFood.intentdefinition
- Intents/OrderFoodIntentHandler.swift

要件：
1. `AppIntent` 準拠の struct に変換（enum ケースは AppEnum に）
2. `@Parameter` でパラメーターを宣言し、Swift ネイティブ型を優先
3. `perform()` を async/throws で実装し、`IntentResult` を返す
4. entity schema・intent schema・view annotations を WWDC 2026 ガイドラインに沿って追加
5. 既存の Intent Extension はそのまま残し、新 App Intents ファイルを別ディレクトリに生成
```

App Intents の `perform()` は async なので `async let` や `withTaskGroup` による並列処理も自然に組み込めます。Claude Code は Swift Concurrency 文脈も理解しているため、`@MainActor` 漏れや `Sendable` 違反も指摘しながら変換してくれます。変換後は Xcode 27 Coding Tools または `xcrun mcpbridge` でそのまま動作確認まで一続きで行えます。
