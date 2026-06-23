---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-06-24"
date: 2026-06-24 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Claude Code v2.1.186（2026-06-22）](https://code.claude.com/docs/en/changelog) — 33 の変更を含む大型マイナーリリース。**`claude mcp login <name>` / `claude mcp logout <name>`** コマンドが追加され、インタラクティブな `/mcp` メニューを開かずに CLI から MCP サーバー認証が完結するようになった（SSH 経由でも `--no-browser` stdin リダイレクトに対応）。また Bash コマンドの実行後に Claude が自動で出力に反応するようになり（`"respondToBashCommands": false` でオフにできる）、`/plugin Installed` タブに「Skills」セクションが追加された。スリープ復帰後の「Content block not found」 / JSON パースエラーも修正され、長時間の `xcodebuild` セッション中に Mac をスリープさせても接続が安定するようになった。

## 🛠 GitHub の動き

- [conorluddy/ios-simulator-skill](https://github.com/conorluddy/ios-simulator-skill) — xc-mcp・xclaude-plugin の作者 conorluddy による、iOS シミュレータ操作に特化したスタンドアロン Agent Skill。27 スクリプトを収録し、xcodebuild をラップしたトークン効率の高い出力（デフォルト 3〜5 行のサマリー）と、アクセシビリティ API を使ったセマンティック UI 操作（ラベル・ID で要素を特定するため座標依存なし）が特徴。`/plugin marketplace add conorluddy/ios-simulator-skill` で導入可能。xc-mcp（29 ツール一体型）や xclaude-plugin（8 モジュール分割型）より軽量で、シミュレータ操作のみ切り出したい場合に向いている。

- [AvdLee/SwiftUI-Agent-Skill](https://github.com/AvdLee/SwiftUI-Agent-Skill) — SwiftLee 作者 Antoine van der Lee による SwiftUI 専用 Agent Skill。状態管理（property wrapper 選択・@Observable・データフロー）・ビュー合成・パフォーマンス・Swift Charts・iOS 26 Liquid Glass・アニメーション（Keyframe / Phase Animator・@Animatable マクロ）を網羅。参照ファイルはオンデマンドでロードするため、必要なセクションのコンテキストだけを消費する設計。同作者の [Swift-Concurrency-Agent-Skill](https://github.com/AvdLee/Swift-Concurrency-Agent-Skill)（Swift 6 準拠・safe concurrency・マイグレーション）・[Swift-Testing-Agent-Skill](https://github.com/AvdLee/Swift-Testing-Agent-Skill)（XCTest からのマイグレーション・Swift Testing）と組み合わせると iOS 開発の 3 本柱（UI / 非同期 / テスト）を一気に補完できる。

- [dpearson2699/swift-ios-skills](https://github.com/dpearson2699/swift-ios-skills) — iOS 26+ / Swift 6.3 向けの Agent Skills 集が v3.0.0 で 83 スキルに拡大（v2.2.0 の 57 → v3.0.0 の 76 → 今回さらに追加）。SwiftUI・Swift Core・iOS App Frameworks・SwiftData・Foundation Models・StoreKit 2・GameKit・Apple Kit フレームワーク（PermissionKit・EnergyKit・AppMigrationKit など iOS/watchOS 26 ベータ API を含む）の 10 バンドルで構成。`npx swift-ios-skills install --all` で全スキルを一括導入できる。Claude Code・Codex・Cursor・Copilot 等 40+ エージェント対応。

## 📝 日本語コミュニティ

- 該当なし（本日時点で iOS × Claude Code の新規記事は確認できず）

## 🌐 海外コミュニティ / Tips

- [SwiftUI Agent Skill: Build better views with AI（SwiftLee / Antoine van der Lee）](https://www.avanderlee.com/ai-development/swiftui-agent-skill-build-better-views-with-ai/) — `AvdLee/SwiftUI-Agent-Skill` の詳細解説記事。ForEach の ID 安定性・NavigationStack への移行・@Observable のグラニュラリティ最適化など、よくある AI 生成 SwiftUI コードの落とし穴とスキルによる改善を実例で示す。オンデマンドロードの仕組みや Cursor・Codex での導入方法も説明。

- [vabole/apple-skills](https://github.com/vabole/apple-skills) — iOS 26+ API に完全特化した Apple 開発スキル集。「LLM のトレーニングデータには legacy Apple API が十分あるため、後方互換が必要なら Agent が追加できる」という方針で Liquid Glass・@Observable・Actor・SwiftData 継承・Foundation Models など最新 API のみを提供。iOS / macOS / watchOS / visionOS 26 対応。Claude Code・Codex 両対応。

## 💡 今日のおすすめ実践 Tip

**「`claude mcp login` で iOS MCP 環境の認証を事前に済ませ、CI / fastlane から Claude Code を無人実行する」**

v2.1.186 の `claude mcp login` コマンドにより、XcodeBuildMCP・App Store Connect MCP・GitHub MCP など OAuth 認証が必要なサーバーへのログインが CLI から完結するようになりました。

```bash
# 1. MCP サーバーの認証状態を確認
claude mcp list

# 2. CI や fastlane から使う MCP サーバーに事前ログイン
claude mcp login appstore-connect
claude mcp login github

# 3. SSH / ヘッドレス環境（CI サーバーなど）でのログイン
claude mcp login appstore-connect --no-browser
# → stdin に認証 URL を貼り付けるフローで完結
```

これまでは MCP サーバーの再認証のたびに `/mcp` インタラクティブメニューを開く必要がありましたが、`claude mcp login` で事前認証を済ませておけば、以下のような無人フローが実現できます。

```bash
# fastlane の before_all フックで Claude Code セッションを起動し、
# TestFlight アップロード後に App Store Connect 経由で審査状況を確認
lane :release do
  gym(scheme: "MyApp")
  pilot(skip_waiting_for_build_processing: true)

  # Claude Code が App Store Connect MCP を使ってメタデータを確認
  sh("claude -p 'App Store Connect MCP を使って最新ビルドの処理状況を確認し、
      審査に出せる状態なら /submit-review を実行して'")
end
```

`claude mcp login appstore-connect` を CI の secrets として認証トークンを設定しておくことで、TestFlight アップロード → 審査提出の最終確認まで Claude Code が無人で担えます。今日紹介した `AvdLee/SwiftUI-Agent-Skill` と `Swift-Concurrency-Agent-Skill` を組み合わせると、「SwiftUI ビュー実装 → Swift 6 concurrency 対応 → Swift Testing でテスト」という iOS 開発の 3 本柱をスキルが支援するセッションをそのまま CI のゲートとして活用することも可能です。
