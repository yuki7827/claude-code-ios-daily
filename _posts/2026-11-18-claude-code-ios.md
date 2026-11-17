---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-11-18"
date: 2026-11-18 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- 該当なし（本日時点で v2.1.325 以降の新規リリースは確認できず。[公式 Changelog](https://code.claude.com/docs/en/changelog) を継続確認中）

## 🛠 GitHub の動き

- [conorluddy/xclaude-plugin（GitHub）](https://github.com/conorluddy/xclaude-plugin) — Xcode・Simulator・IDB をカバーする 8 本のワークフロー特化 MCP サーバー（計 24 ツール）をバンドルした Claude Code 向けマーケットプレイスプラグイン。`/plugin marketplace add conorluddy/xclaude-plugin` でインストール可能で、XcodeBuildMCP を置き換える設計になっており MCP サーバーではなく直接 CLI コマンドを使う点が特徴。UI 自動化は XCUITest 経由で行う。同作者の `ios-simulator-skill` よりも網羅的な構成になっている（先日の 11-17 記事で紹介したスキルと同じ作者の別プロジェクト）。

## 📝 日本語コミュニティ

- [Xcode MCP×Claude Codeプラグインで、iOSビルドを自動化する（Zenn / kyoichi）](https://zenn.dev/kyoichi/articles/claude-code-plugin-xcode-mcp-hybrid) — Apple 公式 Xcode プラグイン（`xcode@claude-plugins-official`）と Apple 純正 Xcode MCP（`xcrun mcpbridge`）を両立させる hybrid 構成の実践記録。プラグインはビルドエラーの自動修正と SwiftUI プレビュー描画を担い、MCP はシミュレーター自動起動とテスト実行を担うという役割分担を整理。片方だけでは届かないカバレッジを両立させるアーキテクチャの考え方がまとまっており、公式 Xcode プラグイン（11-14 記事参照）を既に導入済みの開発者にとって次のステップとなる内容。

- [iOSアプリ開発に役立つAgentic Coding集（Zenn / mtfum）](https://zenn.dev/mtfum/articles/ios_agentic_coding) — SwiftUI / UIKit 開発での Agentic Coding 活用パターンを実例付きでまとめた記事。「PRD → Plan → Implement の 3 ステップサイクルを守る」「大きな変更の前に必ず `/compact` でコンテキストを整理する」「XCUITest の自動化は実装後ではなく実装前に要件として記述する」など、iOS エンジニアが実務で即使える Tips が凝縮されている。

- [App Store用スクリーンショットをClaude Codeのシミュレーター直接操作で作るのは不経済だったのでスクリプト化で解決した（Classmethod）](https://dev.classmethod.jp/articles/claude-code-ios-simulator-screenshot-fastlane/) — Claude Code Desktop の iOS Simulator パネルを使って App Store 用スクリーンショットを撮影すると、1 ロケール分だけで Claude Pro の 5 時間制限の約 38% を消費したというレポート（2026-08-31）。解決策として XCUITest で各画面に遷移する UI テストを書き、`fastlane snapshot` で自動撮影・`fastlane deliver` でアップロードするパイプラインを構築。シミュレーター操作は Claude の「目」ではなく専用スクリプトに任せ、Claude はコードと設定に専念させる分業が推奨されている。

## 🌐 海外コミュニティ / Tips

- [CLAUDE.md for Swift and iOS: 13 Rules that Stop AI from Writing Unsafe, Non-Idiomatic Apple Code（dev.to / Olivia Craft）](https://dev.to/olivia_craft/claudemd-for-swift-and-ios-13-rules-that-stop-ai-from-writing-unsafe-non-idiomatic-apple-code-4nld) — 安全で慣用的な Apple コードを生成するために CLAUDE.md に記述すべき 13 のルールをまとめた記事。「`!` によるフォースアンラップを禁止し `guard let` / `if let` を使う」「`DispatchQueue.main.async` ではなく `@MainActor` を使う」「deprecated な UIKit API（`UIAlertView` 等）は使わない」「`MainActor` の外で `@State` を操作しない」など、コンパイルは通るが iOS 的に問題のあるコードを事前に防ぐガードレールが実用的。

- [10 Tips to Maximize Claude Code for iOS Development（Medium / CodeToDeploy）](https://medium.com/codetodeploy/10-tips-to-maximize-claude-code-for-ios-development-29027edc51a7) — 2026 年 11 月初旬公開。iOS 開発での Claude Code 活用を最大化するための 10 の実践 Tips。「Xcode のビルドスキーム情報を CLAUDE.md に記載する」「シミュレーター操作より実機テストを優先する」「`xcodebuild` のエラーログ全体ではなく要約して渡す」「`#pragma mark` / `// MARK:` を活用して Claude がファイル構造を把握しやすくする」など、Claude Code に仕事を渡す際のコンテキスト最適化が中心。

## 💡 今日のおすすめ実践 Tip

**CLAUDE.md に Swift / iOS 専用の安全ルールを追記する（今すぐできる品質改善）**

dev.to の Olivia Craft 記事にある 13 ルールから特に影響が大きいものを抜粋し、プロジェクトの `CLAUDE.md` の `## Rules` セクションに追記するだけで Claude が生成するコードの品質が上がる。

```markdown
## Rules

### Swift / iOS

- `!` によるフォースアンラップ禁止。`guard let` / `if let` / `guard try` を使うこと
- `DispatchQueue.main.async` は使わない。`@MainActor` または `await MainActor.run` を使うこと
- `UIAlertView`・`UIActionSheet` など deprecated な UIKit API は使わない
- `@State` や `@StateObject` は `MainActor`-isolated なコンテキスト外で変更しない
- `try!` は禁止。`do { try ... } catch { ... }` で必ずエラーハンドリングする
- ハードコードされた認証情報・API キーの生成は禁止。Keychain または環境変数を使うこと
```

これらはいずれも「コンパイルは通るが iOS 的に危険または非推奨」なパターンであり、静的解析（SwiftLint）では検出されないケースもある。CLAUDE.md に書いておくだけで Claude Code が自律的に回避するようになるため、コードレビュー前の問題件数を大幅に減らせる。既存プロジェクトへの追加コストはほぼゼロ。
