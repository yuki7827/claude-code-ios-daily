---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-07-07"
date: 2026-07-07 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [ClaudeForFoundationModels — Anthropic 公式 Swift パッケージ（iOS 27+ / macOS 27+）](https://github.com/anthropics/ClaudeForFoundationModels) — WWDC 2026（2026-06-08）に合わせてリリースされた Anthropic 公式 Swift パッケージ。Apple の Foundation Models フレームワークの `LanguageModel` プロトコルに準拠しており、**オンデバイスモデルと同じ `LanguageModelSession` API** で Claude（Sonnet 5・Opus 4.8 等）を iOS/macOS 27 アプリから呼び出せる。`ClaudeLanguageModel(name: .sonnet5, auth: auth)` を `LanguageModelSession` に渡すだけで、ストリーミング・構造化出力（`@Generable`）・クライアントサイドツール・サーバーサイドツール（Web 検索・コード実行）すべてが Apple と同じ API で動作する。本番アプリはプロキシ経由で API キーを隠す設計（`.proxied(headers:, baseURL:)`）が推奨されており、リクエストは Anthropic に直送（Apple のサーバーを経由しない）。現在ベータ（OS 27 以上必須）。公式ドキュメント: [platform.claude.com](https://platform.claude.com/docs/en/cli-sdks-libraries/libraries/apple-foundation-models)

## 🛠 GitHub の動き

- [rshankras/claude-code-apple-skills — iOS/macOS/iPadOS 向け Apple プラットフォーム開発スキル集](https://github.com/rshankras/claude-code-apple-skills) — iOS・macOS・iPadOS 向けの Claude Code スキルをまとめたリポジトリ。プロダクト検証・コード生成・アクセシビリティレビュー・App Store 最適化・iPad 専用機能・ナビゲーション・移行支援など実務で頻出するドメインをスキル単位でカバー。MCPmarket の「iOS Development Suite」としても公開されており、`/plugin marketplace add rshankras/claude-code-apple-skills` で一括導入できる。`vabole/apple-skills`（07-06 紹介）が iOS 26+ の最新 API に特化しているのに対し、こちらは App Store 申請プロセスや iPadOS 固有の UX パターンも含む幅広い実務カバレッジが特徴。

- [conorluddy/ios-simulator-skill — xcodebuild ラッパー＋シミュレータ操作を統合した iOS Simulator Skill](https://github.com/conorluddy/ios-simulator-skill) — 27 スクリプトで構成される iOS シミュレータ専用 Claude Code Skill。ビルドとシミュレータ操作を単一スキルに統合しており、アクセシビリティ API を使った意味的 UI 操作（座標依存のタップより堅牢）・スクリーンショット解析・ログモニタリング・Hang 検出・アクセシビリティ監査を自律的に実行できる。07-03 に紹介した `xclaude-plugin` がビルドエラー抽出に特化しているのに対し、本スキルはビルド＋シミュレータ操作を両方カバーする点が異なる。「コンテキストを節約するために xcodebuild の詳細ログを内部で処理し、必要な情報だけを Claude に渡す」設計は共通。

- [keskinonur/claude-code-ios-dev-guide — PRD 駆動 × XcodeBuildMCP 統合 iOS 開発の総合ガイド](https://github.com/keskinonur/claude-code-ios-dev-guide) — Swift/SwiftUI iOS 開発に最適化した Claude Code CLI セットアップ手順書。インストール・CLAUDE.md テンプレート・PRD 駆動ワークフロー・Ultrathink（extended thinking）・プランモード・XcodeBuildMCP 統合・カスタムスラッシュコマンド・サブエージェント・フック・パーミッション管理など 17 トピックを網羅。「Swift の MVVM + `@Observable` + async/await + strict concurrency」を前提とした設定テンプレートが同梱されており、iOS プロジェクトへの Claude Code 導入を ゼロから体系立てて進めたい開発者向け。

## 📝 日本語コミュニティ

- [Apple Foundation Models が Claude・Gemini を同じ Swift API で呼べる（Qiita / okssusucha）](https://qiita.com/okssusucha/items/6811ea41135d83220a49) — WWDC 2026 で Apple が Foundation Models フレームワークを**プロバイダー非依存の抽象レイヤー**として再設計した点を解説。同一の `LanguageModelSession` API でオンデバイスモデル・Private Cloud Compute・Claude（Anthropic）・Gemini（Google）を切り替えられる設計思想と、Swift Package Manager での導入手順をまとめた記事。「難しいタスクは Claude に、軽量タスクはオンデバイスで」という切り替えロジックを実装例付きで解説。iOS 27 beta を使えるチームが最初に読む資料として有用。

- [iPad から Claude Code をスキル選択で操作するアーキテクチャ（Zenn / sika7）](https://zenn.dev/sika7/articles/54074477b83049) — Claude Code を VPS 上でヘッドレスホストし、iPad の SwiftUI UI からスキルを選んでコード生成・テスト・コミットを自律実行させるアーキテクチャを解説した記事（2026-04-20）。フロー：iPad SwiftUI（スキル選択 + 自然言語タスク入力）→ HTTPS/SSE → Laravel API（CLI ブリッジ）→ Claude Code CLI → SSE ストリームで結果を iPad に返す。SwiftUI 側の `URLSession.bytes` を使った SSE クライアントのコード例も掲載。「スキルを選ばせることでプロンプトの自由記述より誤作動が減り、許可ツールの範囲を明示的に絞れる」という設計思想は iOS 開発文脈でも有益。

## 🌐 海外コミュニティ / Tips

- [Claude support for Apple's Foundation Models framework（Anthropic 公式ブログ）](https://claude.com/blog/claude-for-foundation-models) — Anthropic が ClaudeForFoundationModels パッケージのリリースと共に公開した公式解説記事。「オンデバイスモデルが速度・プライバシー・オフライン対応に優れる一方、大きなコンテキスト・フロンティア推論・サーバーサイドツール（Web 検索・コード実行）が必要なときは Claude にエスカレートする」という棲み分けを提示。Apple のオンデバイスモデルとクラウド側の Claude を同一 API で使い分けるユースケース（複雑な機能設計・リアルタイム情報検索・コード生成サポート等）が iOS アプリ内で自然に設計できるようになった意義を解説。

- [Apple Foundation Models Framework 2026: Claude & Gemini（Nerd Level Tech）](https://nerdleveltech.com/apple-foundation-models-framework-claude-gemini) — Apple Foundation Models の `LanguageModel` プロトコルを介して Claude と Gemini をプロバイダーとして切り替える方法を比較解説した英語記事。WWDC 2026 で公開された iOS 27 の Foundation Models サーバーサイド言語モデル API の仕様をまとめており、`ClaudeLanguageModel` / `GoogleGeminiLanguageModel` を `LanguageModelSession` に差し込むだけで、ほぼコード変更なしにプロバイダーを切り替えられる点を実証。`SwiftPM` での依存追加から動作確認まで手順が整理されており、iOS 27 beta 環境を持つ開発者が試す際の参考になる。

## 💡 今日のおすすめ実践 Tip

**「ClaudeForFoundationModels で iOS 27 アプリに "賢さの切り替えスイッチ" を組み込む」**

`ClaudeForFoundationModels` を使うと、iOS 27 アプリ内でオンデバイスモデルと Claude を **コード変更なし** で切り替えられます。軽量タスクはデバイス上で素早く・重いタスクは Claude で確実に、という設計を Swift の型安全な API で実現できます。

**1. Package.swift に追加**

```swift
// Package.swift
dependencies: [
  .package(url: "https://github.com/anthropics/ClaudeForFoundationModels.git", from: "0.1.0")
]
```

**2. ViewModel での使い分けパターン**

```swift
import FoundationModels
import ClaudeForFoundationModels

class MyViewModel: ObservableObject {
    // 軽量タスク：オンデバイスモデル（高速・プライバシー重視）
    private lazy var localModel = SystemLanguageModel.default
    // 複雑なタスク：Claude（大コンテキスト・推論が必要な処理）
    private lazy var claudeModel = ClaudeLanguageModel(
        name: .sonnet5,
        auth: .proxied(headers: ["X-App-Token": userToken]),
        baseURL: URL(string: "https://api.yourbackend.com/claude")!
    )

    func processQuery(_ query: String, complexity: QueryComplexity) async throws -> String {
        let model: any LanguageModel = complexity == .simple
            ? localModel : claudeModel
        let session = LanguageModelSession(model: model)
        let response = try await session.respond(to: query)
        return response.content
    }
}
```

**3. サーバーサイドツールで Web 検索を自動追加**

```swift
// 最新情報が必要なクエリには Web 検索を有効化
let claudeWithSearch = ClaudeLanguageModel(
    name: .sonnet5,
    auth: auth,
    serverTools: [.webSearch(maxUses: 3)]
)
```

**設計のポイント**

| 用途 | 推奨モデル | 理由 |
|------|-----------|------|
| テキスト補完・短文要約 | オンデバイス | 速度・コスト・オフライン |
| コード生成・複雑な質問応答 | Claude Sonnet 5 | 大コンテキスト・精度 |
| 最新情報を含む回答 | Claude + Web 検索ツール | オンデバイスは学習データに依存 |
| 深い推論が必要 | Claude Opus 4.8 + xhigh effort | フロンティア推論 |

**本番での API キー管理注意点**

`.apiKey("...")` は開発専用です。App Store 向けには必ずプロキシサーバーを立て `.proxied(headers:, baseURL:)` を使用してください。キーをバイナリに埋め込むとリバースエンジニアリングで抽出されるリスクがあります。
