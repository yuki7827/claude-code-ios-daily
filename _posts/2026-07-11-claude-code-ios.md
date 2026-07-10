---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-07-11"
date: 2026-07-11 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Claude Code v2.1.206（2026-07-09）— `/doctor` CLAUDE.md トリミング提案・バックグラウンドエージェント自動アップグレード・MCP タイムアウト修正](https://code.claude.com/docs/en/changelog) — 27 の変更（新機能 9・バグ修正 18）が含まれる。iOS 開発者に直結する主な変更点は 3 つ。① **`/doctor` が CLAUDE.md のトリミングを提案する機能を追加** — コードベースを読めば Claude が自力で推測できる内容を `/doctor` が検出し、削除候補として提示する。iOS プロジェクトの CLAUDE.md に埋め込みがちな「ファイル構成の逐一説明」「Swift バージョンの繰り返し明記」などを整理でき、プロンプトキャッシュ効率の向上とトークン削減が期待できる。② **バックグラウンドエージェントが Claude Code 更新後にバックグラウンドで自動アップグレード** — 従来はアタッチ時にアップグレードが走り数十秒のラグがあったが、更新直後にバックグラウンドで自動完了するようになった。夜間 iOS ビルドエージェントを複数並行させている場合、朝のアタッチが即座に行えるようになる。③ **MCP の `request_timeout_ms` が `--mcp-config` / `.mcp.json` で指定しても無視されていたバグを修正** — これまで XcodeBuildMCP のビルドや `xcodebuild archive` のような長時間ツール呼び出しがデフォルト 60 秒でタイムアウトし「なぜか途中で止まる」現象の原因の一つだった。`request_timeout_ms` を適切な値（例：300000ms）に設定することでタイムアウトを回避できるようになる。

- [anthropics/ClaudeForFoundationModels — Apple Foundation Models フレームワーク経由で Claude を iOS/macOS アプリに統合できる Swift パッケージ](https://github.com/anthropics/ClaudeForFoundationModels) — WWDC 2026 で Apple が発表した Foundation Models フレームワーク（`LanguageModel` プロトコル）の最初のサードパーティ実装として Anthropic が公開した Swift パッケージ。iOS 27 / macOS 27 / visionOS 27 / watchOS 27 beta 向け（Apache 2.0）。`ClaudeLanguageModel(name: .sonnet5, auth: .apiKey(...))` と宣言するだけで Apple の `LanguageModelSession` API から Claude を呼び出せる。`session.respond(to:)` / `session.streamResponse(to:)` / `@Generable` による型安全な構造化出力・ウェブ検索/フェッチ/コード実行のサーバーサイドツールにも対応しており、オンデバイスモデルと同一の Swift API で Claude の高性能モデルをアプリに統合できる。リクエストは Apple を経由せずアプリから直接 Claude API へ送られる設計で、プライバシー要件の整理がしやすい。

## 🛠 GitHub の動き

- [conorluddy/ios-simulator-skill — トークン 97.5% 削減・セマンティック UI 操作の iOS シミュレータ特化スキル](https://github.com/conorluddy/ios-simulator-skill) — 27 本のスクリプトで構成された iOS シミュレータ操作特化スキル。生の `xcodebuild` 出力をそのまま Claude に渡すのではなく `Build: SUCCESS (0 errors, 3 warnings) [xcresult-20250318]` のような 1 行サマリーに圧縮し、コンテキスト消費を最大 97.5% 削減する設計が特徴。UI 操作は iOS アクセシビリティ API を活用したセマンティック要素特定（ピクセル座標不要）で安定性が高い。最新の v1.4.0 ではプラグインマーケットプレイスに対応し `/plugin marketplace add conorluddy/ios-simulator-skill` で導入可能。**Model Inspector** 機能（`.xcdatamodeld` パッケージと `@Model` Swift 宣言のパーサー）が追加され、SwiftData モデルの構造を Claude がプロジェクト横断的に把握できるようになった。07-08 に紹介した `kylehughes/apple-platform-build-tools-claude-code-plugin` が Agent Skill＋Subagent の 2 層構造でコンテキスト効率化を図るのに対し、本スキルは「シミュレータ操作そのもの」のコスト最小化に特化している。

- [rshankras/claude-code-apple-skills — iOS/macOS/watchOS/visionOS 向け 147 スキル × 23 カテゴリ](https://github.com/rshankras/claude-code-apple-skills) — Apple プラットフォーム開発全般を網羅した大型スキルコレクション（494 スター）。63 本のコード生成スキル（ネットワーク層・認証・ペイウォール・ロギング・Analytics 連携など）と、14 本の製品開発スキル（アイデア検証 → 競合分析 → PRD 生成 → App Store 登録まで）、App Store 最適化（ASO）・キーワード最適化・レビュー対応、Apple Intelligence（Foundation Models フレームワーク）・Liquid Glass（iOS 26 の新デザイン言語）・SwiftData・visionOS への対応スキルも含む。`/plugin marketplace add rshankras/claude-code-apple-skills && /plugin install apple-skills@indie-apple-stack` で一括インストールできる。07-10 に紹介した `twostraws/swift-agent-skills`（Swift 言語・フレームワーク別の細粒度整理）と比較すると、本コレクションはコード生成〜App Store 出荷の実務フロー全体をカバーする点が補完的で、両方を併用する用途に向いている。

## 📝 日本語コミュニティ

- [LLM × MCP で iOS アプリの UI テストを自動化してみた話（Zenn / mikantechnology）](https://zenn.dev/mikantechnology/articles/d5c3eaafec134b) — 語学学習 iOS アプリ「Mikan」の開発チームが、Claude Code ＋ XcodeBuildMCP ＋ iOS-simulator-MCP サーバーを組み合わせてシミュレータ上の UI テストを自動化した実録。Claude Code がシミュレータへのアプリインストール・起動・単語学習レッスンの進行・スクリーンショット取得・Markdown テストレポート生成まで一連の流れを自律実行した。安定動作の鍵は「`accessibilityLabel` による意味的な要素特定」で、ピクセル座標ベースのタップではなく「正解の選択肢」「次へ進む」のような自然言語でボタンを識別させることでシミュレータ解像度や UI 変更に強いフローを実現した。課題として「1 回の UI 操作ごとに画面状態を LLM が読んで判断するため、トークン消費が多い」点が挙げられており、conorluddy/ios-simulator-skill のような出力圧縮レイヤーとの組み合わせが今後の改善策として示唆されている。

- [【コピペで使える】Claude Code のカスタムエージェント 7 選 — 個人アプリ開発者が今すぐ作るべき .md ファイル全公開（Qiita / kotaro_ai_lab）](https://qiita.com/kotaro_ai_lab/items/b6bb6e3c67f66eeede6b) — `.claude/agents/` に Markdown ファイルとして置くカスタムエージェントを、iOS 個人アプリ開発の文脈で 7 種類のテンプレートとともに公開した記事。`ios-ui-reviewer`（SwiftUI ビューのアクセシビリティ・HIG 準拠チェック）・`crash-analyzer`（クラッシュログ解析と修正提案）・`release-drafter`（App Store リリースノート自動生成）・`localization-helper`（.strings ファイルの翻訳候補生成）・`swift-refactor`（Swift 6 対応リファクタリング）・`testflight-checklist`（審査前チェックリスト生成）・`aso-optimizer`（キーワード・説明文の ASO 提案）の 7 種。各エージェントに `model: claude-opus-4-8`（高精度が必要な ASO/クラッシュ解析）と `model: claude-haiku-4-5`（繰り返し実行する localization）を使い分けるコスト設計が参考になる。

## 🌐 海外コミュニティ / Tips

- [Apple's LanguageModel Protocol: Xcode 27 Just Made Model Lock-In Optional（Developers Digest）](https://www.developersdigest.tech/blog/apple-languagemodel-protocol-xcode-27-model-lock-in) — `ClaudeForFoundationModels` の登場が意味する「Apple エコシステムにおけるモデル選択の自由化」を論じた記事。Apple が `LanguageModel` プロトコルを公開したことで、オンデバイス（Apple Intelligence）・クラウド（Claude / Gemini）をアプリ側コードを変えずにスワップできるアーキテクチャが実現したと指摘。`DependencyInjection` パターンで `LanguageModelSession` を抽象化する実装例を示しており、「テスト時はオンデバイスモデル、本番は Claude Sonnet 5」のような切り替えが 1 行で済む設計になる。iOS 27 beta での動作検証が中心だが、Foundation Models の一般提供（2026 年秋予定）を見据えたアーキテクチャ設計の参考として今から読んでおく価値がある。

- [Anthropic Ships ClaudeForFoundationModels — Swift Native Claude for iOS 27 Apps（AI/TLDR）](https://ai-tldr.dev/releases/anthropic-claude-for-foundation-models/) — `anthropics/ClaudeForFoundationModels` のリリース概要と iOS 開発者向けのクイックスタートをまとめた英語記事。`@Generable` マクロを使った型安全な構造化出力の実装例（例：`@Generable struct Recipe { let title: String; let steps: [String] }`）が具体的で、Apple の on-device `@Generable` と完全に同一の API を Claude にも使えることが示されている。**リクエストはアプリ → Claude API の直接通信**（Apple のサーバーを経由しない）という点について、App Store レビューガイドラインやプライバシーポリシーへの記載方法についても言及されており、実際の App Store 申請に向けた準備ポイントを押さえた記事になっている。

## 💡 今日のおすすめ実践 Tip

**「`ClaudeForFoundationModels` で iOS 27 アプリに Claude を組み込む — 最小実装から `@Generable` 構造化出力まで」**

`anthropics/ClaudeForFoundationModels` を使うと、Apple の Foundation Models フレームワークと同じ `LanguageModelSession` API で Claude を iOS 27 アプリに統合できます。以下に最小実装から応用までをまとめます。

**1. Package.swift に依存追加**

```swift
// Package.swift
dependencies: [
    .package(
        url: "https://github.com/anthropics/ClaudeForFoundationModels.git",
        from: "0.1.0"
    )
],
targets: [
    .target(
        name: "MyApp",
        dependencies: ["ClaudeForFoundationModels"]
    )
]
```

**2. 基本的な応答取得**

```swift
import FoundationModels
import ClaudeForFoundationModels

// 開発時は APIキー直接指定、本番はプロキシ経由を推奨
let model = ClaudeLanguageModel(
    name: .sonnet5,
    auth: .apiKey(ProcessInfo.processInfo.environment["ANTHROPIC_API_KEY"] ?? "")
)
let session = LanguageModelSession(model: model)

// 通常応答
let response = try await session.respond(to: "この SwiftUI のコードをレビューして：\n\(code)")
print(response.content)

// ストリーミング応答
for try await partial in session.streamResponse(to: "App Store の説明文を 170 字以内で書いて") {
    print(partial.content, terminator: "")
}
```

**3. `@Generable` で型安全な構造化出力**

```swift
import FoundationModels

// Apple on-device モデルと完全に同じ API
@Generable
struct AppReviewAnalysis {
    let sentiment: String       // "positive" | "neutral" | "negative"
    let mainIssue: String
    let suggestedImprovements: [String]
}

let analysis = try await session.respond(
    to: "以下のユーザーレビューを分析して：\n\(reviewText)",
    generating: AppReviewAnalysis.self
)
print(analysis.sentiment)      // "negative"
print(analysis.suggestedImprovements)  // ["クラッシュを修正", "起動速度の改善"]
```

**4. モデルを DI で差し替えられる設計（テスト対応）**

```swift
// LanguageModel プロトコルで抽象化
class AppService {
    private let session: LanguageModelSession

    // テスト時はオンデバイスモデル、本番は Claude
    init(model: any LanguageModel = ClaudeLanguageModel(name: .sonnet5, auth: ...)) {
        self.session = LanguageModelSession(model: model)
    }
}
```

**注意点（App Store 申請前に確認）**

| 項目 | 内容 |
|------|------|
| リクエスト経路 | アプリ → Claude API（Apple を経由しない） |
| プライバシーポリシー | 「第三者 AI サービス（Anthropic）にデータを送信します」の明記が必要 |
| iOS 最低バージョン | iOS 27 beta（GA は 2026 年秋予定） |
| API キー保護 | 本番はプロキシサーバー経由（`auth: .proxied`）を使用し、アプリバイナリに埋め込まない |

Foundation Models の一般提供は 2026 年秋予定です。今 beta で実装を試しておくことで、リリース時に即座に対応できます。Claude Code で実装を進め、`/doctor` で CLAUDE.md を最適化しながら開発すると、プロジェクトの説明コンテキストが適切に保たれ、より精度高く Claude の支援を受けられます。
