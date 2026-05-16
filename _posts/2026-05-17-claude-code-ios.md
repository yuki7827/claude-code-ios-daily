---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-05-17"
date: 2026-05-17 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Claude Code v2.1.143 (May 15)](https://code.claude.com/docs/en/changelog) — **プラグイン依存関係の強化**が目玉。`claude plugin disable` が依存するプラグインを持つ場合は拒否（無効化チェーンのヒント付き）し、`claude plugin enable` は推移的依存関係を自動有効化するようになった。`worktree.bgIsolation: "none"` 設定を追加し、バックグラウンドセッションが worktree を作らず作業コピーを直接編集できる構成が可能に。バックグラウンドセッションのモデル・エフォートレベルがスリープ/ウェイク後も保持されるよう修正。MCP OAuth トークン刷新の競合状態・`/goal` バックグラウンドシェル実行中の誤発火も解消。iOS チームが XcodeBuildMCP プラグインを依存関係として管理する場合に直接影響する変更。

## 🛠 GitHub の動き

- [getsentry/XcodeBuildMCP v2.5.2 (May 12)](https://github.com/getsentry/XcodeBuildMCP) — iOS/macOS プロジェクト向け MCP サーバー兼 CLI。xcodebuild ベースで動作するため Xcode プロセス不要。スキーム・シミュレータの自動検出・Swift Macro ビルドエラー回避を含む。v2.5.2 が 2026 年 5 月 12 日時点の最新安定版（累計 55 リリース）。Claude Code の MCP 設定に追加するだけでビルド・テスト・シミュレータ管理を自律実行可能。
- [vabole/apple-skills](https://github.com/vabole/apple-skills) — Claude Code / Codex 向け Apple プラットフォーム開発スキル集（231 スター）。iOS 26+ API・SwiftUI・UIKit・**Liquid Glass**・Human Interface Guidelines に対応したリファレンスをスキルとして注入できる。Swift 6 の `@Observable` マクロや `NavigationStack` を前提に設計し、古いパターンを排除した最新慣習ベースの生成を実現。`ios-liquid-glass` スキルと `ios-design-consultant` スキルを使うと iOS 26 新デザインシステム準拠コードを生成可能。

## 📝 日本語コミュニティ

- [Xcode26.3 で MCP が追加され Claude Code から SwiftUI のレンダリングができるようになった](https://zenn.dev/zaico/articles/98a1528d07d195) (zaico, Zenn) — Xcode 26.3 RC の `xcrun mcpbridge` を Claude Code から使い、SwiftUI プレビューをターミナルで直接レンダリングする手順を実録。`RenderPreview` ツールが SwiftUI キャンバスの PNG を返すため、コード修正→見た目確認のループが CLI だけで完結する点を紹介。UI 実装の精度が大幅に向上した体験談も含む。
- [Claude Code から Xcode が提供する MCP に接続する方法](https://zenn.dev/pepabo/articles/4c0d1019ac1f7d) (ペパボエンジニア, Zenn) — `xcrun mcpbridge` の起動フロー・Claude Code の MCP 設定への追加方法・Xcode 許可ダイアログの対処まで手順を図解。Apple 純正ブリッジ経由で `BuildProject`・`RunAllTests`・`RenderPreview` の 3 ツールが Claude Code 側で利用可能になることを確認した実践レポート。

## 🌐 海外コミュニティ / Tips

- [Agentic Coding in Xcode 26.3 with Claude Code and Codex](https://swiftjectivec.com/Agentic-Coding-Codex-Claude-Code-in-Xcode/) (Swiftjective-C / Jordan Morgan) — Xcode 26.3 の Claude Agent と Codex を実際に試した詳細レポート。IDE 内エージェントが SwiftUI ビューを自律的に実装→プレビューキャプチャ→修正するループの精度と限界を率直に評価。CLI の Claude Code と Xcode 内エージェントの使い分け基準（大規模リファクタは CLI、UI 微調整は IDE 内）を提示。
- [Xcode 26.3 + Claude Agent — Model Swapping, MCP, Skills, and Adaptive Configuration](https://fatbobman.com/en/posts/xcode-263-claude/) (Fatbobman) — Swift 界隈で知名度の高い Fatbobman（東坡肘子）による技術解説。Xcode 26.3 内の Claude Agent でモデル切り替え・MCP 統合・スキル追加・アダプティブ設定を体系的に検証。XcodeBuildMCP と Apple 純正 mcpbridge の併用構成、スキルのスコープ管理（グローバル vs プロジェクト）など実運用で詰まる設定ポイントをカバー。

## 💡 今日のおすすめ実践 Tip

**`vabole/apple-skills` で iOS 26 Liquid Glass 対応コードを Claude Code から安全に生成する**

Apple が iOS 26 で導入した Liquid Glass デザインシステムは学習コストが高く、Claude の学習データでは最新 API のカバーが薄い領域です。`vabole/apple-skills` の `ios-liquid-glass` スキルを追加すると、最新のリファレンスドキュメントをコンテキストに注入した状態でコード生成が走り、古いパターン（`NavigationView`・`UINavigationController` 直接操作など）を返すリスクが下がります。

インストールと基本的な使い方の例:

```bash
# プラグインとしてインストール（Claude Code 公式プラグイン管理経由）
claude plugin install vabole/apple-skills

# あるいは .claude/skills/ に直接クローン
git clone https://github.com/vabole/apple-skills .claude/skills/apple-skills
```

インストール後、Claude Code に以下のように指示するとスキルが自動的にコンテキストに読み込まれます:

```
既存の NavigationView ベースのコードを NavigationStack + Liquid Glass スタイルに移行してください。
iOS 26+ の glassEffect モディファイアを使い、Swift 6 / Xcode 26.3 でコンパイルできる形にしてください。
```

`ios-design-consultant` スキルを合わせて使うと、生成コードが Human Interface Guidelines の Liquid Glass 適用ガイドラインに沿っているかを Claude が自己レビューするフローになります。まず小さなコンポーネント（タブバー・ナビゲーションバー）から移行を試し、`RenderPreview`（Xcode mcpbridge）で見た目を確認しながら進めるのが安全な進め方です。
