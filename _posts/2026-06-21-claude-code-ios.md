---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-06-21"
date: 2026-06-21 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Claude Code v2.1.185（2026-06-20）](https://code.claude.com/docs/en/changelog) — マイナーリリース。API レスポンス待機時のメッセージが「No response from API · Retrying in …」から「Waiting for API response · will retry in …」に変更され、リトライのトリガータイミングが 10 秒から 20 秒に延長された。長時間の `xcodebuild` ビルドが走る iOS CI 環境で不要なリトライノイズが出にくくなる。

## 🛠 GitHub の動き

- [haider-nawaz/liquid-glass-skill（★21）](https://github.com/haider-nawaz/liquid-glass-skill) — iOS 26+ / macOS 26+ の Liquid Glass 対応に特化した Claude Code スキル。既存アプリの移行を 5 フェーズのワークフローで自動化し、`.glassEffect()` / `.backgroundExtensionEffect()` / `GlassEffectContainer` など API を正確に適用できる。Apple の Landmarks サンプルから抽出した実コードパターンや既知の落とし穴と対処法を収録。`/plugin marketplace add haider-nawaz/liquid-glass-skill` で導入可能。

- [conorluddy/LiquidGlassReference（★306）](https://github.com/conorluddy/LiquidGlassReference) — 同じく conorluddy 氏（xc-mcp の作者）による iOS 26 Liquid Glass の Ultimate Swift/SwiftUI リファレンスドキュメント。基礎から GlassEffectContainer・モーフィング遷移・シンボルエフェクト・パフォーマンス最適化・アクセシビリティまでを網羅した単一ドキュメントで、Claude に Liquid Glass 実装を依頼する際の参照ソースとして指定して使う設計。

- [greenstevester/fastlane-skill](https://github.com/greenstevester/fastlane-skill) — Fastlane セットアップを Claude Code に一任できるスキル集（fastlane/fastlane Discussion #29838 で紹介）。「"Set up Fastlane for my iOS app"」と指示するだけで `.pbxprojg` からバンドル ID・チームを自動検出して Appfile・Fastfile を生成。Match（証明書管理）・Snapshot（App Store スクリーンショット自動化）・Beta（TestFlight アップロード）・Release（App Store 申請）の 5 スキルを収録。MIT ライセンス。

## 📝 日本語コミュニティ

- [Xcode MCP×Claude Codeプラグインで、iOSビルドを自動化する（Zenn / kyoichi）](https://zenn.dev/kyoichi/articles/claude-code-plugin-xcode-mcp-hybrid) — Apple 純正 Xcode MCP（`mcpbridge` 経由の 20 ツール）と XcodeBuildMCP（82 ツール）をプラグインシステム経由でハイブリッド構成にする手順を解説。Xcode 起動中は `mcpbridge` を優先し、CI などヘッドレス実行では XcodeBuildMCP にフォールバックする設計で、claude-code-plugin 形式での導入も説明。

## 🌐 海外コミュニティ / Tips

- [Agentic Coding in Xcode 26.3 with Claude Code and Codex（Swiftjective-C）](https://swiftjectivec.com/Agentic-Coding-Codex-Claude-Code-in-Xcode/) — Xcode 26.3 のネイティブ Agent SDK 統合のもとで Claude Code と Codex を使い分ける実践的な考察記事。バックグラウンドタスク・サブエージェント・プラグインを Xcode 内から操作する具体的なフローや、複数エージェントの使い分け基準（コード生成は Claude、ビルド検証は XcodeBuildMCP など）をエンジニア目線でまとめている。

## 💡 今日のおすすめ実践 Tip

**「Claude を Liquid Glass の "実装職人" に仕立てる：LiquidGlassReference 指定 + liquid-glass-skill の 2 段構成」**

iOS 26 対応で Liquid Glass 移行が求まれる場面では、Claude が古い情報でハルシネーションを起こしやすい。今日紹介した 2 つのリソースを組み合わせると、API を正確に適用した移行が実現できます。

```bash
# 1. Liquid Glass スキルを導入
/plugin marketplace add haider-nawaz/liquid-glass-skill

# 2. LiquidGlassReference を参照ドキュメントとしてプロジェクトに配置
curl -sSL https://raw.githubusercontent.com/conorluddy/LiquidGlassReference/main/LiquidGlassReference.md \
  > .claude/context/liquid-glass-reference.md
```

Claude Code への指示例:

```
.claude/context/liquid-glass-reference.md を参照しながら、
liquid-glass スキルを使って ContentView.swift を
iOS 26 の Liquid Glass デザインに移行して。

移行の流れ:
1. 既存のカスタム背景・シャドウ・角丸を洗い出す（フェーズ1: 監査）
2. 安全に置き換えられる箇所から .glassEffect() に差し替える（フェーズ2: 基礎移行）
3. ツールバー・ナビゲーションバーを Glass ネイティブスタイルに更新する（フェーズ3: コンポーネント）
4. GlassEffectContainer でモーフィング遷移を適用する（フェーズ4: アニメーション）
5. アクセシビリティとダークモードを検証する（フェーズ5: 検証）

XcodeBuildMCP でビルドし、各フェーズ完了後に構造化エラーを確認して自律修正して。
```

`conorluddy/LiquidGlassReference` の正確な API リファレンスを`.claude/context/` に置いておくことで、Claude がハルシネーションした API（例：存在しない `GlassMaterial` など）を使うリスクを大幅に下げられます。`haider-nawaz/liquid-glass-skill` のフェーズワークフローと組み合わせると、既存アプリ全体の Liquid Glass 移行を段階的かつ安全に進められます。
