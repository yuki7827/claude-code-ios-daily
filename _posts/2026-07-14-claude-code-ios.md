---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-07-14"
date: 2026-07-14 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- 該当なし — [Claude Code changelog](https://code.claude.com/docs/en/changelog) の最新版は v2.1.207（07-11）のまま、本日（07-14）時点で新リリースなし。週間利用上限 50% 増プロモーションは本日 10AM JST に終了。

## 🛠 GitHub の動き

- [Issue #36151 — FEATURE: Claude Mobile アプリでメールアドレスが異なる複数アカウントを切り替えたい（open・👍 453 件・❤️ 119 件・合計 577 件）](https://github.com/anthropics/claude-code/issues/36151) — 個人用 Pro/Max と会社用 Team/Enterprise を別メールアドレスで運用している iOS 開発者が、iPhone の Claude アプリで毎回 sign-out → sign-in しなければならない問題。Desktop App 向けの同種 Issue（#18435）と並行しており、モバイル feature request の中で最多クラスの反応数。07-13 にもコメントが追加されており要望の熱量が高い。現状の回避策は「一方のアカウントは Claude アプリ、もう一方はモバイルブラウザの claude.ai」という使い分け。

- [conorluddy/xclaude-plugin — iOS 開発向け Claude Code プラグイン: 8 MCP サーバー × 24 ツールでトークン効率化](https://github.com/conorluddy/xclaude-plugin) — 同作者の `ios-simulator-skill`（07-11 紹介）の上位互換プラグイン。Xcode・Simulator・IDB（iOS Device Bridge）のツールをワークフロー別に 8 つの MCP サーバー（xc-build / xc-test / xc-launch / xc-ui など）へ分割し、現在のタスクに必要な MCP だけをアクティブにすることでコンテキスト消費を最小化する。ビルドログ・テスト結果・クラッシュ情報を 50+ 行の生出力ではなく構造化 JSON で Claude に渡す設計で、エラー特定の精度と速度が向上。

- [keskinonur/claude-code-ios-dev-guide — PRD 駆動 × ultrathink × 計画モード最適化の Swift/SwiftUI 開発セットアップガイド](https://github.com/keskinonur/claude-code-ios-dev-guide) — Claude Code CLI を Swift/SwiftUI iOS 開発に特化してセットアップするガイドリポジトリ。PRD（製品要件定義書）駆動ワークフロー・拡張思考（`/ultrathink`）の活用・計画モードの使い分けを重点解説。CLAUDE.md のテンプレート・XcodeBuildMCP との接続設定・`/goal` による自律ループ設計もカバーしており、iOS 開発向け Claude Code のゼロベース環境構築に使える。

## 📝 日本語コミュニティ

- [Swift × Kiro × ClaudeCode（Zenn / jambo_dev）](https://zenn.dev/jambo_dev/articles/34fe2c1db1ae05) — Amazon Kiro の Spec モードで `requirements.md` / `design.md` / `tasks.md` を生成し、その仕様書ファイルを Claude Code に渡して Swift/SwiftUI の実装を行う分業ワークフローを解説。「Kiro が曖昧さを排除してから Claude Code に渡すことで、仕様不明確によるリグレッションを防ぎつつ Claude Code の実装速度を活かせる」という要点。Kiro の Spec モードを iOS 開発の要件定義フェーズに適用した実践記事。

- [KiroとClaude Codeの組み合わせで開発の質と速度を両取りできた（Zenn / ubie_dev）](https://zenn.dev/ubie_dev/articles/kiro-claude-code) — Kiro（仕様確定・設計）と Claude Code（高速実装）の役割分担により「品質と速度の両立」を実現したという開発チームの実践報告。コスト面では Kiro Spec モードが約 1.6 倍かかるが、自動生成されるテスト数が大幅に多い（78 件 vs 15 件）と比較。SwiftUI アプリ開発での要件整理に Kiro を活用し、コード生成は Claude Code に任せるハイブリッドフローに応用できる。

## 🌐 海外コミュニティ / Tips

- [Two MCP Servers Made Claude Code an iOS Build System（Blake Crosley）](https://blakecrosley.com/blog/xcode-mcp-claude-code) — XcodeBuildMCP（82 ツール: ビルド・テスト・シミュレータ・LLDB デバッグ）と Apple 公式 Xcode MCP（20 ツール: ファイル操作・診断・SwiftUI Preview）を組み合わせて Claude Code を iOS ビルドシステム化した構成ガイド。生の `xcodebuild` ログ解析を構造化 JSON レスポンスに置き換えることでエラー特定精度が向上し、Claude が「ビルドエラー → 修正 → 再ビルド」のループを安定して自律実行できるようになると紹介。

- [Amazon Kiro vs Claude Code 2026 Deep Review — Kiro iOS アプリによる iPhone からの Spec 駆動開発操作（WeavAI Blog）](https://weavai.app/blog/en/2026/05/24/amazon-kiro-vs-claude-code-2026-ai-ide-deep-review/) — AWS Summit NY 2026（2026 年 6 月）で発表した Kiro iOS アプリを取り上げた比較レビュー。Claude Code の公式 Remote Control（iPhone から Mac 上の Claude Code を操作）と競合する位置づけで、Kiro iOS アプリでは Spec モードの仕様書作成・タスク承認を iPhone から実施できる。「要件定義フェーズは Kiro iOS アプリ、コード実装は Mac の Claude Code」というハイブリッド運用が iOS 開発者に選択肢として生まれている。

## 💡 今日のおすすめ実践 Tip

**「`conorluddy/xclaude-plugin` でワークフロー別 MCP を使い分け、トークン消費を最小化する」**

`ios-simulator-skill` と比べて xclaude-plugin はビルド・テスト・UI 操作を MCP 単位で切り分けているため、「今何をしているか」に合わせて有効にする MCP を絞れます。

**1. プラグインのインストール**

```bash
# Claude Code プラグインマネージャからインストール
claude plugin add conorluddy/xclaude-plugin
```

**2. ワークフロー別 MCP サーバーの使い分け**

| 作業フェーズ | 有効にする MCP | 説明 |
|------------|-------------|-----|
| ビルド確認 | `xc-build` | Xcode ビルドの実行・エラーの JSON 化 |
| テスト実行 | `xc-test` | Swift Testing / XCTest の結果構造化 |
| シミュレータ起動 | `xc-launch` | アプリのインストール・起動 |
| UI 自動操作 | `xc-ui` | アクセシビリティベースのタップ・入力 |
| デバイスログ確認 | `xc-logs` | クラッシュログ・os_log の収集 |

**3. Claude Code セッション内での使い方**

```bash
# ビルドフェーズのみ（xc-build だけ有効）
"Build MyApp targeting iPhone 15 simulator and show me any errors."

# ビルド → 起動 → UI テストの一連フロー
"Build MyApp, launch it on iPhone 15 Pro simulator, 
then verify that the onboarding flow completes without errors.
Use xc-build, xc-launch, and xc-ui."
```

**4. Kiro + Claude Code 分業フローとの組み合わせ**

Kiro Spec モードで仕様書（requirements.md / tasks.md）を作成した後、Claude Code に `tasks.md` を読ませて実装 → `xc-build` で検証 → `xc-test` でテスト という流れをひとつのセッションで実行できます。

```
# Claude Code への指示例
"Read tasks.md and implement the tasks one by one.
After each task, run xc-build to verify, 
then run xc-test to check test coverage."
```

`xclaude-plugin` の最大のメリットは「必要な MCP だけを Claude の tool list に載せる」点で、全ツールを常時有効にした場合と比べてコンテキスト圧迫を抑えられます。大規模 iOS プロジェクトで Claude Code を長時間走らせる際に特に効果的です。
