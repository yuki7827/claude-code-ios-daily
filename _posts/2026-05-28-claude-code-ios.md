---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-05-28"
date: 2026-05-28 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Claude Code v2.1.152](https://github.com/anthropics/claude-code/releases) (May 27) — iOS 開発者向けに注目の変更が複数。**`/code-review --fix`** がレビュー後にワーキングツリーを直接修正するように（`/simplify` もこれを呼ぶ形に統合）。スキルのフロントマターに **`disallowed-tools`** を記述するとそのスキル実行中だけ特定ツールを無効化できる（Xcode ビルドスキルで不要な Bash ツールを制限するなど）。**`/reload-skills`** コマンドで再起動なしにスキルディレクトリを再スキャン可能に。`SessionStart` フックがセッションタイトルを設定できるようになり、CI 上での Claude Code ジョブ識別が容易になった。

## 🛠 GitHub の動き

- [patrickserrano/skills](https://github.com/patrickserrano/skills) — iOS/Swift/SwiftUI 向け Claude Code スキル集（10 種）。`ios-debugger-agent`（シミュレータビルド・実行・デバッグ）、`swift-concurrency-expert`（Swift 6.2+ 並行処理レビュー）、`swiftui-liquid-glass`（iOS 26+ Liquid Glass API 実装）、`swiftui-performance-audit`（レンダリング・メモリ診断）など、リリースワークフロー（App Store チェンジログ生成・macOS SPM パッケージング）まで網羅。

- [dpearson2699/swift-ios-skills](https://github.com/dpearson2699/swift-ios-skills) — iOS 26+/Swift 6.3 に特化した 84 スキル（v3.4.0、4 月 26 日）。SwiftUI・Core Data・CloudKit・AI/ML フレームワーク・ハードウェア統合まで 9 カテゴリに整理。Claude Code のほか Cursor・GitHub Copilot にも対応し、スキルは独立設計で個別インストール可能。

- [greenstevester/fastlane-skill](https://github.com/greenstevester/fastlane-skill) — fastlane セットアップを Claude Code で自動化するスキル（v1.1.2、5 月 10 日）。Xcode プロジェクトから Bundle ID・Team ID を自動検出して Fastfile を生成し、TestFlight ベータ配布・Match 証明書管理・Snapshot スクリーンショット生成を自然言語指示で実行。fastlane 未経験者でも App Store 配布フローを一通り構築できる設計。

## 📝 日本語コミュニティ

- [iPad から Claude Code を Skill 選択付きで操作するアーキテクチャ](https://zenn.dev/sika7/articles/54074477b83049) (sika7, Zenn、4 月 20 日) — VPS 上で動かした Claude Code を SwiftUI アプリ経由で iPad から操作するアーキテクチャを解説。スキル一覧を取得・表示して選択実行する UI を SwiftUI で実装しており、外出先でのコーディングセッション管理に活用できる構成。

- [VS Code の Claude Code で「iOS コードレビュー → 修正 → Xcode ビルド確認」を自動化する（第 1 弾）](https://qiita.com/4q_sano/items/f29dba5fa92958850a32) / [第 2 弾（テスト項目作成まで）](https://qiita.com/4q_sano/items/effe51de5e9654777f1e) (4q_sano, Qiita) — VS Code 拡張版 Claude Code を使い、コードレビュー → 修正提案 → xcodebuild ビルド確認 → テスト項目生成まで一気通貫に自動化するワークフローを初心者向けに解説した 2 部作。Xcode を開かずに Swift コードのビルド検証を回す手順が具体的。

## 🌐 海外コミュニティ / Tips

- [Claude Code skill for automated Fastlane setup (fastlane Discussion #29838)](https://github.com/fastlane/fastlane/discussions/29838) — fastlane 公式リポジトリのディスカッションに Claude Code スキルが取り上げられた。xcodebuild プロジェクト設定を自動解析して Fastfile・Matchfile・Appfile を生成し、初回セットアップを大幅短縮する提案。TestFlight アップロード・App Store 申請・スクリーンショット生成の各 lane を自然言語で記述できる点が注目されている。

- [TestFlight Expert Claude Code Skill](https://mcpmarket.com/tools/skills/testflight-expert) (mcpmarket.com) — TestFlight のベータ管理に特化したスキル。内部・外部テスターグループの管理、App Store Connect API 連携、ビルドアップロードを Claude Code から制御できる。CI/CD パイプラインへの組み込みを想定した設計で、fastlane と組み合わせると配布フロー全体を自動化可能。

## 💡 今日のおすすめ実践 Tip

**`disallowed-tools` でスキル実行時の余計なツール呼び出しを防ぐ（v2.1.152 新機能）**

v2.1.152 から、スキルのフロントマターに `disallowed-tools` を記述すると、そのスキルが有効な間だけ特定ツールを無効化できます。iOS ビルドスキルではシェルコマンドの誤爆を防ぎ、コードレビュースキルでは書き込み系ツールを封じるといった使い方が有効です。

```markdown
---
description: "iOS ビルド確認スキル"
disallowed-tools:
  - Write
  - Edit
  - Bash
---

xcodebuildMCP の build_sim ツールだけを使って
iOS アプリをビルドし、エラーを JSON で報告します。
```

`disallowed-tools` と組み合わせる活用パターン：

- **コードレビュースキル**: `Write` / `Edit` を封じて読み取り専用に固定。意図しない自動修正を防止。
- **TestFlight 配布スキル**: `Read` 系ツールのみ許可し、Bash での破壊的操作をブロック。
- **UI 検証スキル**: `WebFetch` を封じてネットワーク呼び出しを防ぎ、ローカルシミュレータのみに集中。

`/reload-skills` コマンドを使えば、スキルファイルを編集した直後にセッションを再起動せず反映できます。
