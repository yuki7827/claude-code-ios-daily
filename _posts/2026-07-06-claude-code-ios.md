---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-07-06"
date: 2026-07-06 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- 該当なし（本日時点の最新リリースは v2.1.201 / 2026-07-03。変更内容は [07-05 記事](https://yuki7827.github.io/claude-code-ios-daily/) 参照）

## 🛠 GitHub の動き

- [twostraws/swiftui-agent-skill — Paul Hudson（Hacking with Swift）による SwiftUI 専用エージェントスキル](https://github.com/twostraws/swiftui-agent-skill) — VoiceOver に認識されないボタン・非推奨 API の使用・パフォーマンス問題など、LLM が苦手な SwiftUI の落とし穴に特化したスキル。「何千時間もの SwiftUI 実践知識」を凝縮しており、`npx skills add https://github.com/twostraws/swiftui-agent-skill --skill swiftui-pro` でインストール後、`/swiftui-pro` を呼び出すだけで Claude Code のコードレビュー精度が向上する。Claude Code・Codex・Gemini 等に対応。

- [twostraws/swift-agent-skills — Swift 全般をカバーするエージェントスキルのキュレーション集](https://github.com/twostraws/swift-agent-skills) — SwiftUI・SwiftData・Swift Concurrency・Swift Testing・アクセシビリティ・Core Data・WidgetKit・App Store Connect など幅広いドメインのスキルを一か所にまとめたキュレーションリポ。「インストール前に中身を必ず読み、著者を信頼できるか確認すること」と注意書きがあり、各スキルが独立しているため必要なものだけ選んで導入できる。

- [dpearson2699/swift-ios-skills — iOS 26+ / Swift 6.3 対応の 84 スキルバンドル](https://github.com/dpearson2699/swift-ios-skills) — SwiftUI（Liquid Glass・NavigationStack・アニメーション等 10 スキル）・Swift コア・App Experience フレームワーク・AI/ML（Foundation Models 等）・iOS エンジニアリング（デバッグ・App Store 審査対策等）など 9 カテゴリ 84 スキルを収録。`all-ios-skills` バンドルで全スキルをまとめてロードするほか、ドメイン別バンドルで必要な範囲に絞ることでコンテキスト消費を抑えられる設計。

- [haider-nawaz/liquid-glass-skill — iOS 26+ Liquid Glass 移行・新規実装専用スキル](https://github.com/haider-nawaz/liquid-glass-skill) — Apple の Liquid Glass デザインシステム（iOS 26+/macOS 26+）への既存アプリ移行を 5 フェーズで自動化するスキル。`.glassEffect()` / `.backgroundExtensionEffect()` / `GlassEffectContainer` / `glassEffectID()` など主要 API の正しい使い方とよくある落とし穴の回避策を組み込み済み。WWDC 2025 の Landmarks サンプルと公式セッションをベースに構築されており、`/plugin marketplace add` で即導入可能。

## 📝 日本語コミュニティ

- [コードを1行も書かずに25,000行のiOSアプリを作った話 — Claude Codeで完全 Vibe Coding（Qiita / john-rocky）](https://qiita.com/john-rocky/items/b5a24858e797c954b16f) — 155 ファイル・25,000 行超の iOS アプリを 11 日間（2 月 15〜26 日）かけて Claude Code だけで実装した実録。著者自身はコードを 1 行も書かずに完成させており、どのシーンで Claude Code が詰まったか・どう突破したかのリアルな体験が率直に語られている。Vibe Coding の可能性と限界を両方示す資料として参考になる。

- [Xcode MCP × Claude Code プラグインで、iOS ビルドを自動化する（Zenn / kyoichi）](https://zenn.dev/kyoichi/articles/claude-code-plugin-xcode-mcp-hybrid) — Xcode 26.3 の純正 MCP（RenderPreview・DocumentationSearch 等の Xcode 専用ツール）と Claude Code プラグイン型 MCP（XcodeBuildMCP 等）を組み合わせてビルド・プレビュー・ドキュメント検索を一括自動化する構成を解説。純正 MCP でしか使えない SwiftUI Preview のレンダリングと、XcodeBuildMCP が得意なビルドログ構造化を組み合わせることで、それぞれの長所を活かしたハイブリッド運用ができる。

- [SwiftData × Claude Code で永続化層を設計する — @Model 設計からマイグレーションまで（Qiita / kotaro_ai_lab）](https://qiita.com/kotaro_ai_lab/items/119901d60f34e07da801) — SwiftData の `@Model` クラス設計・リレーションシップ・マイグレーションスキーマを Claude Code に委任する際の実践ガイド。Claude Code が間違えやすいマイグレーション定義の落とし穴と `swift-ios-skills` の SwiftData スキルを組み合わせた回避策を中心に解説。永続化層を一から Claude Code に任せて実務に使える品質にするための要点が整理されている。

## 🌐 海外コミュニティ / Tips

- [SwiftUI Agent Skill — Write better SwiftUI code with Claude, Codex, and other AI tools（Hacking with Swift / Paul Hudson）](https://www.hackingwithswift.com/articles/282/swiftui-agent-skill-claude-codex-ai) — SwiftUI の苦手な落とし穴（VoiceOver 不可視ボタン・deprecated API・パフォーマンスアンチパターン等）をカバーするエージェントスキルを Paul Hudson が公開したことを解説した記事。Claude Code・Codex・Gemini 等で共通して使える設計で、インストール方法と `/swiftui-pro` コマンドの使い方を図解付きで紹介。Paul Hudson は Hacking with Swift の主宰者であり、このスキルの信頼性は高い。

- [vabole/apple-skills — iOS 26+ 専用 Apple 開発スキル集（Liquid Glass API リファレンス付き）](https://github.com/vabole/apple-skills) — `@Observable`・`NavigationStack`・`async/await`・Liquid Glass など iOS 26+/Swift 6 の最新パターンに特化した Apple 開発スキル集。旧来の `ObservableObject` や `NavigationView` などのレガシーパターンを意図的に除外し、「エージェントはレガシー API を既に知っているはずなので、現代的な作法だけを教える」設計思想を採用。twostraws・AvdLee・Dimillian など Swift 教育界の著名人によるワークフローガイドも含まれる。

## 💡 今日のおすすめ実践 Tip

**「`liquid-glass-skill` + SwiftUI スキルで既存 iOS アプリを iOS 26 デザインへ移行する」**

iOS 26 の Liquid Glass デザインへの移行は、どの API をどの順で適用するかの判断が難しく、Claude Code に任せるとハマりやすいポイントでもあります。専用スキルを入れることでこの問題を一気に解消できます。

**1. スキルのインストール**

```bash
# Liquid Glass 移行スキル
/plugin marketplace add haider-nawaz/liquid-glass-skill

# SwiftUI 全般のレビュー精度向上
npx skills add https://github.com/twostraws/swiftui-agent-skill --skill swiftui-pro
```

**2. 移行フローの例**

```
# Claude Code への指示
/liquid-glass:liquid-glass "ContentView.swift から始めて、
既存の ZStack + Color.black.opacity(0.3) のオーバーレイを
iOS 26+ の .glassEffect(.regular) に移行して。
移行できないビューは TODO コメントを付けて残して。
完了後に /swiftui-pro でアクセシビリティと
deprecated API を確認して。"
```

**3. 主要な API 対応表（スキルが自動参照）**

| 旧パターン | iOS 26+ Liquid Glass 相当 |
|-----------|--------------------------|
| `.background(.ultraThinMaterial)` | `.glassEffect(.regular)` |
| `ZStack { color.opacity(0.2) }` | `GlassEffectContainer` |
| `.buttonStyle(.bordered)` | `.buttonStyle(.glass)` |
| `NavigationView` | `NavigationStack` + glass toolbar |

`haider-nawaz/liquid-glass-skill` は WWDC 2025 の公式セッションと Landmarks サンプルをベースに「よくある移行ミス」を網羅しているため、Claude Code が誤った API を生成するリスクが大幅に下がります。iOS 26 を ターゲットに追加した段階で、まずこのスキルを有効にしてからビュー移行を始めるのがおすすめです。
