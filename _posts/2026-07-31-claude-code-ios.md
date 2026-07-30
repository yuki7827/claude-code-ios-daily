---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-07-31"
date: 2026-07-31 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- 新リリースなし（v2.1.220（2026-07-25）が最新。2026-07-26 記事で取り上げ済み）
- [anthropics/ClaudeForFoundationModels v0.1.0（GitHub）](https://github.com/anthropics/ClaudeForFoundationModels) — Anthropic が公式公開している Swift Package。Apple の Foundation Models フレームワークの `LanguageModel` プロトコルを実装し、`LanguageModelSession` という単一の API で「Apple オンデバイスモデル」「Claude Sonnet 5」「Claude Opus 5」を引数1つで切り替えられる。iOS 27 / macOS 27 / visionOS 27 beta 対象、Apache 2.0 ライセンス。現在 beta（API は GA 前に変更される可能性あり）。

## 🛠 GitHub の動き

- [Issue #82586 — CLI のバックグラウンドタスクが iOS アプリで閲覧中に誤 kill される（OPEN / 2026-07-30）](https://github.com/anthropics/claude-code/issues/82586) — Claude Code CLI v2.1.220 で `run_in_background: true` の Bash タスクを走らせている最中、同セッションを iOS アプリの Code タブで閲覧していたところ、最大 4 件のタスクが連続してユーザー操作なしに kill された問題。harness 内部の `stopTask` RPC が bridge 経由で iOS クライアントから届いているとみられ（TaskStop ツール呼び出しは transcriptにない）、30〜60 分以内に 4 件発生してその後は再現せず。直近の 7/28 記事の push 通知不達（Issue #82374）に続き、「iOS アプリで眺めているだけで CLI セッションに副作用が出る」問題が立て続けに報告されている。本日の Tip で回避策を紹介する。

- [Issue #82468 — モバイルアプリの許可プロンプトのボタンが縦並びで誤タップしやすい（OPEN / 2026-07-30）](https://github.com/anthropics/claude-code/issues/82468) — iOS アプリのツール実行許可プロンプト（Allow / Always allow / Deny / Chat）が全幅の縦 4 ボタンで表示されるため、スマホ上では隣接ボタンとの距離が短く、特に「Always allow」と「Deny」を誤タップするリスクが高い UX バグ。報告者は「Allow と Deny を画面の左右に置いた 2×2 グリッドにしてほしい」と提案。7/30 記事で取り上げた Issue #81250（キーボード重なりで誤タップ）と合わせて、iPhone でのパーミッション操作の信頼性向上を求める声が続いている。

## 📝 日本語コミュニティ

該当なし（本日時点で直近 5 投稿（2026-07-26〜30）と重複しない新規の iOS / Swift / Xcode 関連記事は Zenn・Qiita ともに確認できなかった）

## 🌐 海外コミュニティ / Tips

- [Using Claude with Apple Foundation Models（artemnovichkov.com）](https://artemnovichkov.com/blog/using-claude-with-apple-foundation-models) — `ClaudeForFoundationModels` パッケージを実際の iOS アプリに組み込む手順を解説した実践記事。`LanguageModelSession` の `respond(to:)` から `streamResponse(to:)` へのリファクタリング例、`@Generable` による型付き出力、App Attest 認証（`Secure Enclave` を使ったデバイス証明でアプリにキーを埋め込まない方式）の Xcode 設定手順が参考になる。特に App Attest は Simulator 非対応（物理デバイス専用）なので、開発中は `auth: .apiKey(...)` を使いシミュレーター反復 → App Attest に切り替えて実機配布という 2 段階の運用が現実的。

- [Foundation Models, Year Two: From On-Device API to General LLM Runtime（ivanmagda.dev）](https://ivanmagda.dev/posts/wwdc26-foundation-models-year-two/) — WWDC 2026 で Foundation Models フレームワークがオンデバイス専用 API から汎用 LLM ランタイムに拡張された経緯を技術面からまとめた記事。Apple 公式の `LanguageModel` プロトコルが Claude・Gemini・ローカル MLX モデルも受け入れる設計になった背景、`@Generable` / `@Guide` による型付き出力の仕組み、Claude を使う場合のプライバシー上の注意点（Apple のサーバーはリクエストパスに入らず、使用量は Anthropic アカウントに課金）を整理している。iOS 27 が一般リリースされると、App Store アプリが Claude を一般ユーザーに提供するルートが実質的に整備されたことになる。

## 💡 今日のおすすめ実践 Tip

**「iOS アプリでセッションを閲覧中に CLI のバックグラウンドタスクが誤 kill されるのを防ぐ（Issue #82586 暫定対策）」**

Issue #82586 の調査では、iOS アプリの Code タブでセッションを開いているときに bridge 経由の `stopTask` RPC が誤発火し、ユーザーが何も操作していないのに CLI バックグラウンドタスクが終了するケースが報告されています。XcodeBuildMCP の長時間ビルドや複数ステップのサブエージェントパイプラインを走らせながら iPhone でモニタリングする場合に特に影響が出ます。

**現時点の推奨対策 3 点：**

1. **長時間タスク中は iOS アプリを閉じる（最も確実）**  
   問題が iOS クライアントの `stopTask` RPC 誤発火と疑われているため、タスク実行中は iPhone の Code タブを閉じてホーム画面に戻ることでリスクが下がります。タスク完了後に開き直して最終メッセージを確認するワークフローに切り替えてください。

2. **CLAUDE.md に再起動指示を入れておく**  
   kill された場合でも次の応答で状況を再確認できるよう、CLAUDE.md に以下を追記しておくと実害が小さくなります：
   ```markdown
   ## バックグラウンドタスクが中断された場合
   killed または stopped と報告されたバックグラウンドタスクは、ユーザーの意図による中断ではない可能性がある。
   実行状態をチェックしてから、必要であれば自律的に再実行してよい。
   ```

3. **プッシュ通知 + 手動確認の組み合わせ**  
   7/30 記事の Issue #82374（プッシュ通知不達）と組み合わさると「通知が来ない → セッションを開きに行く → stopTask が誤発火する」の悪循環になります。重要なジョブは通知を待たず一定間隔で Code タブを開き直すのではなく、デスクトップ通知（ターミナル）を主チャネルとして使ってください。
