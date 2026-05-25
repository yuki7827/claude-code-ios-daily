---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-05-26"
date: 2026-05-26 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

該当なし — 本日時点で新規リリースなし（最新は v2.1.150 / 5月23日）。

## 🛠 GitHub の動き

- [conorluddy/ios-simulator-skill](https://github.com/conorluddy/ios-simulator-skill) — iOS Simulator 専用の Claude Code スキル（27 スクリプト）。xcodebuild のビルド・テストラップと xcrun simctl / idb 経由のシミュレータ操作（セマンティック UI ナビゲーション・アクセシビリティ監査・デバイスライフサイクル管理）を提供。長い xcodebuild ログによるコンテキスト消費をプログレッシブ開示設計で抑制。`/plugin marketplace add conorluddy/ios-simulator-skill` でインストール可能。

- [vermont42/ios-build-verify](https://github.com/vermont42/ios-build-verify) — SwiftUI iOS アプリの「ビルド → UI 検証」ループを完結させる Claude Code スキル。xcodebuild + xcbeautify でトークン効率よくビルド・テストし、Cameron Cooke 製 AXe CLI と xcrun simctl の組み合わせでアクセシビリティツリーベースの UI 検証・タップ・スクリーンショットを実行。スクリーンショットより AXe の `describe-ui` テキストを優先することでトークン消費を最小化し、Claude Opus 4.7 で検証済み。

- [rshankras/claude-code-apple-skills](https://github.com/rshankras/claude-code-apple-skills) — Apple プラットフォーム開発向けの Claude Code スキル集（149 スキル・23 カテゴリ）。コード生成（ログ・分析・ネットワーク・認証など 52 種）、プロダクトワークフロー（アイデア検証〜App Store 申請 13 工程）、iOS・macOS・テスト・セキュリティ・マネタイズの専門スキルを一括提供。個人開発者のフルサイクルをカバーする設計。

- [artemnovichkov/xcode-26-system-prompts](https://github.com/artemnovichkov/xcode-26-system-prompts) — Xcode 26 に内蔵された AI エージェントのシステムプロンプトと内部ドキュメントを収集したリポ。後述の Jack Pearce の調査と合わせて参照することで、Xcode Claude エージェントの挙動制約・利用可能なツール・CLAUDE.md で上書きできるパラメータの範囲が把握できる。

## 📝 日本語コミュニティ

- [Claude Codeにシミュレータを渡したら自分でタップしてスクショで検証し始めた](https://qiita.com/shimo4228/items/3c6106d5407deea0cbf4) (shimo4228, Qiita) — Claude Code と XcodeBuildMCP の UI 操作ツール（tap・screenshot・snapshot_ui）をシミュレータに向けたところ、Claude が自律的に画面をタップして表示内容をスクリーンショットで確認し始めたプロセスを記録。`.accessibilityIdentifier("quiz_choice_A")` のようなアクセシビリティ ID を付与しておくことで UI ツリーからタップ対象を特定できる仕組みを解説。

- [iOS アプリのテスト結果を確認する Claude Code スキルを作ってみた](https://qiita.com/ddpi/items/401ead85348a100df240) (ddpi, Qiita) — Xcode のテスト結果（xcresult）を Claude Code から読み込んで解析するカスタムスキルの自作事例。テスト失敗時の差分を Claude が解析して原因と修正案を出力するフローを構築し、CI との組み合わせでテスト→修正ループを自律化する方向性を示している。

- [非エンジニアがClaude Codeだけで、iOSアプリをApp Storeに出した話](https://zenn.dev/naoki_dev/articles/098a689c4acdd5) (naoki_dev, Zenn) — プログラミング未経験の著者が Claude Code のみを使って iOS アプリを完成させ App Store にリリースした実録。ビルドエラー・プロビジョニング・スクリーンショット提出など詰まりやすいポイントと、Claude Code への効果的な指示の出し方を整理した記録。

## 🌐 海外コミュニティ / Tips

- [Xcode 26.3 + Claude Agent: Model Swapping, MCP, Skills, and Adaptive Configuration](https://fatbobman.com/en/posts/xcode-263-claude/) (fatbobman) — Xcode 26.3 同梱の claude バイナリ（`~/Library/Developer/Xcode/CodingAssistant/Agents/Versions/26.3/`）を最新 CLI に差し替えて Opus 4.6 を Xcode Agent として使う手法を解説。MCP 設定がグローバル設定を引き継がないため絶対パスが必須な点と、`CLAUDE_CONFIG_DIR` で Xcode 内/CLI 環境を判別する「Environment Awareness」CLAUDE.md の書き方も紹介。

- [Extracting Xcode 26.3's Claude Code Prompt](https://www.jackpearce.co.uk/posts/extracting-xcodes-claude-code-prompt/) (Jack Pearce) — Xcode 26.3 内蔵の claude バイナリを解析し、システムプロンプトの構造・利用可能な環境変数（`CLAUDE_CODE_EFFORT_LEVEL` / `CLAUDE_CODE_ENABLE_CFC` 等）を抽出した調査。Hacker News でも取り上げられた。`artemnovichkov/xcode-26-system-prompts` と組み合わせることで、Xcode Agent の挙動を事前にモデル化して CLAUDE.md を設計できる。

## 💡 今日のおすすめ実践 Tip

**`.accessibilityIdentifier` を仕込んでおくと Claude がシミュレータを自律操作できる**

`vermont42/ios-build-verify` や XcodeBuildMCP の AXe ツールで UI 検証ループを回す場合、SwiftUI ビューに `.accessibilityIdentifier` を事前に付与しておくことが自動検証の鍵になります。アクセシビリティ ID があると、Claude が `snapshot_ui` で UI ツリーを読んで「どのボタンをタップすべきか」を構造データから判断できるため、フラジャイルな座標指定に依存しません。

```swift
// 検証対象の各コントロールに ID を付ける
Button("次へ") { nextStep() }
    .accessibilityIdentifier("next_button")

Text(viewModel.errorMessage)
    .accessibilityIdentifier("error_label")
```

この状態で Claude Code に「`next_button` をタップして `error_label` の表示内容を確認してください」と依頼するだけで、シミュレータ上の操作・確認ループが自律的に回ります。`ios-build-verify` は AXe の `describe-ui` テキストを優先し、視覚確認が必要なときだけスクリーンショットを撮るため、トークン消費も最小限に抑えられます。
