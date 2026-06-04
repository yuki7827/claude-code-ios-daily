---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-06-05"
date: 2026-06-05 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Claude Code v2.1.162（2026-06-03）](https://code.claude.com/docs/en/changelog) — `claude agents --json` に `waitingFor` フィールドが追加され、待機中セッションがどのプロンプト（権限確認など）で止まっているかを JSON で確認可能に。Remote Control がセッション起動時の一時メッセージから**常駐フッターピル**（セッションリンク付き）に変更され、モバイルから常時監視しやすくなった。スラッシュコマンドのオートコンプリートはメニュークリックで「即実行」→「プロンプトに補完」に動作変更（Enter キーで実行）。`/effort` がデフォルト保持される場合に確認メッセージを表示。UI 名称上 "Windsurf" が "Devin Desktop" に改名。

## 🛠 GitHub の動き

- [AvdLee/SwiftUI-Agent-Skill v3.4.0（2026-05-28）](https://github.com/AvdLee/SwiftUI-Agent-Skill) — Antoine van der Lee（SwiftLee ブログ著者）作の SwiftUI ベストプラクティス専門スキル、★3,000 超。v3.4.0 では `xctrace` ラッパーのパフォーマンス診断ツールを同梱し、メインスレッドのハング・ヒッチを CPU 使用率と相関させて JSON で出力できる。iOS 26+ Liquid Glass エフェクトの参照ガイドも収録。Claude Code プラグイン・Cursor・Codex いずれにも対応し、`npx skills add` でインストール可能。

- [twostraws/swift-agent-skills](https://github.com/twostraws/swift-agent-skills) — *Hacking with Swift* の Paul Hudson がキュレートするスキルカタログリポジトリ、★1.9k。以前紹介した `SwiftUI-Agent-Skill`（単一スキル）とは別の**ディレクトリ型**リポジトリで、SwiftUI / SwiftData / Swift Concurrency / Swift Testing / アクセシビリティ / App Intents / パフォーマンス / セキュリティなど 15+ カテゴリにまたがる個別スキルのリンク集。インストール前にスキルを人間がレビューするよう注意喚起している点も特徴。

## 📝 日本語コミュニティ

- [Xcode 26.3 で iOS 開発の AI 活用はどう変わるのか ── タップルでの導入方針とあわせて](https://developers.cyberagent.co.jp/blog/archives/62551/)（CyberAgent Developers Blog）— Xcode 26.3 以前は Claude Code に自律的なビルド〜修正ループを組むのが困難だったが、Apple 公式 MCP サーバー（`xcrun mcpbridge`）により外部エージェントから Xcode のビルド・テスト・SwiftUI Preview を操作できるようになった変化を整理。マッチングアプリ「タップル」iOS チームの段階導入方針（何を Claude に任せて何を人間が見るか）も紹介しており、プロダクション iOS チームへの導入を考える際の参考になる。

## 🌐 海外コミュニティ / Tips

- **WWDC 2026 まで 72 時間**（Keynote: 6/8 月・日本時間 6/9 午前 2 時）— iOS 27 / macOS 27 の Developer Beta が即日公開予定。Foundation Models フレームワークの on-device 推論 API 拡充と、新 Siri Extensions（App Intents 深化）が Claude Code との組み合わせ開発に直接影響する。[dpearson2699/swift-ios-skills（★687・v3.4.0）](https://github.com/dpearson2699/swift-ios-skills) には AI & ML カテゴリ 5 スキル（Core ML・Vision・Speech Recognition・Natural Language・Foundation Models）が既に収録されており、Beta 後の新 API 対応への土台に使える。

- [The Complete Guide to Building an iOS App with Claude Code (No Xcode Required)](https://dev.to/marypiakovski/the-complete-guide-to-building-an-ios-app-with-claude-code-no-xcode-required-o5b)（dev.to / marypiakovski）— Xcode GUI を使わずに Claude Code CLI + XcodeBuildMCP のみで iOS アプリをビルドから App Store 提出まで行う手順書。PRD 駆動ワークフロー・シミュレータ自動テスト・App Review 対策の全工程をカバーし、Xcode に不慣れなバックエンド開発者が iOS 開発に入るための入門として有用。

## 💡 今日のおすすめ実践 Tip

**WWDC 直前: AvdLee/SwiftUI-Agent-Skill の xctrace 分析でパフォーマンスベースラインを記録する**

WWDC 2026 で iOS 27 Developer Beta が来る前に、現行 iOS 26 アプリのパフォーマンスベースラインを測定しておくと、Beta 適用後の退行検出が容易になります。`AvdLee/SwiftUI-Agent-Skill` v3.4.0 の `xctrace` ラッパーを使えば Claude Code から直接計測できます。

```bash
# スキルをプロジェクトに追加
npx skills add https://github.com/AvdLee/SwiftUI-Agent-Skill --skill swiftui-pro
```

Claude Code への指示例:

```
swiftui-pro スキルを使って、MyApp の iPhone 16 シミュレータ上で
起動シーケンス（Launch → ホーム画面表示）の xctrace 記録を 3 回実行し、
ハング（メインスレッド 250ms 以上のブロック）とヒッチ（フレームドロップ）を
JSON で報告してください。結果を docs/perf-baseline-ios26.json に保存してください。
```

iOS 27 Beta リリース後に同一コマンドを再実行し、`docs/perf-baseline-ios26.json` と比較することで API 変更による退行を即座に特定できます。Foundation Models の on-device 推論を組み込んでいるアプリは、新 OS でのメモリ圧力変化によるヒッチ増加が起きやすいため、事前のベースライン計測が特に重要です。
