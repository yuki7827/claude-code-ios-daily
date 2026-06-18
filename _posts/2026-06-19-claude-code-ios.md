---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-06-19"
date: 2026-06-19 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Claude Code v2.1.181（2026-06-17）](https://code.claude.com/docs/en/changelog) — macOS 向けに重要な修正と新機能を追加。`sandbox.allowAppleEvents` opt-in 設定が追加され、サンドボックス内から Apple Events を送信できるようになった（Xcode MCP の `open` / `osascript` / ブラウザ認証がエラー -600 で失敗していた問題が解消）。また `/config key=value` 構文でプロンプトから任意設定を切り替えられるようになり（インタラクティブ・`-p`・Remote Control 全モード対応）、Spotlight リインデックス中に macOS TUI が応答不能になる不具合も修正された。iOS 開発でサンドボックスを有効にしたまま Xcode MCP を動かしたい場合には直接の恩恵になる。

## 🛠 GitHub の動き

- [ApptitudeLabs/claude-code-ios-dev-setup](https://github.com/ApptitudeLabs/claude-code-ios-dev-setup)（40 コミット） — iOS エンジニア向けの "definitive setup" ガイド。XcodeBuildMCP・Axiom 50+ スキル・Swift 6 strict concurrency・ultrathink（拡張思考）・PRD-driven workflow・カスタムスラッシュコマンドを一通り網羅し、「汎用 AI 開発ガイドを iOS に転用したもの」ではなく iOS エンジニアのために設計した点を強調。iOS 26 / Swift 6 / Xcode 15+ 対応。

- [kylehughes/apple-platform-build-tools-claude-code-plugin](https://github.com/kylehughes/apple-platform-build-tools-claude-code-plugin)（v2.0.0） — `xcodebuild`・Swift Package Manager・`simctl`・`devicectl` など xcrun ツール群を網羅したクイックリファレンス付きの Agent Skill と、プロジェクト発見・コマンド構築・エラーハンドリングを担う Subagent の 2 ピース構成。プラグインマーケットプレイスか `.claude/settings.json` で導入可能。iOS / macOS / tvOS / watchOS / visionOS のディスティネーション指定テーブルも収録。MIT ライセンス。

- [hmohamed01/swift-development](https://github.com/hmohamed01/swift-development)（★59） — SwiftUI パターン（Observable / ObservableObject）・NavigationStack・Swift 6 並行処理（async/await・Actor）・XCTest / Swift Testing・シミュレータ管理スクリプトを同梱したシンプルな Claude Code スキル。SwiftFormat / SwiftLint 設定テンプレートやパッケージテンプレートも収録。Xcode 15+ / Swift 6 対応。

## 📝 日本語コミュニティ

- [【第2弾】VS CodeのClaude Codeで「iOSコードレビュー → 修正 → Xcodeビルド確認 → テスト項目作成」を自動化する（Qiita / 4q_sano）](https://qiita.com/4q_sano/items/effe51de5e9654777f1e) — 06-18 で紹介した「Claude Code標準Skill × iOS」の第2弾。VS Code 上で Claude Code を使い、Swift / UIKit コードのレビューから修正・Xcode ビルド確認・テスト項目生成まで一連のフローを自動化する具体的な手順を紹介。Claude Code と VS Code の組み合わせで Xcode を直接操作せずに iOS 開発を完結させる方向性を示している。

## 🌐 海外コミュニティ / Tips

- [Xcode 26.3 + Claude Agent — Model Swapping, MCP, Skills, and Adaptive Configuration（fatbobman.com）](https://fatbobman.com/en/posts/xcode-263-claude/) — Xcode 26.3 で使える Claude Agent SDK の「モデル切り替え」「MCP 接続」「Agent Skills」「適応的設定」の 4 機能を実践的に解説。`mcpbridge` が MCP を XPC 経由で Xcode ライブプロセスに橋渡しする仕組みや、Claude / Apple ローカルモデル / Gemini をプロジェクト依存変更だけで切り替えられる設計を詳述。英語記事だが説明が丁寧でわかりやすい。

## 💡 今日のおすすめ実践 Tip

**「`sandbox.allowAppleEvents: true` で、サンドボックスを維持したまま Xcode MCP をエラーなしで動かす」**

v2.1.181 の `sandbox.allowAppleEvents` は 1 行の追加で、Xcode 連携で頻発していたエラー -600 を根本解決します。

```json
{
  "sandbox": {
    "enabled": true,
    "allowAppleEvents": true
  },
  "permissions": {
    "allow": [
      "Bash(command:xcodebuild*)",
      "Bash(command:xcrun simctl*)",
      "Bash(command:open*)"
    ]
  }
}
```

この設定で解決される問題:

- Xcode MCP の `BuildProject` / `RenderPreview` / `RunSomeTests` が Apple Events 経由で Xcode ライブプロセスに接続する際のエラー -600
- `osascript` を使った Xcode のダイアログ自動承認スクリプトがサンドボックスでブロックされていた問題
- ブラウザベースの認証フロー失敗

サンドボックスを無効にしてエラー回避していた方は `allowAppleEvents: true` でサンドボックスを維持したまま Xcode MCP が動きます。06-17 で紹介した `Tool(param:value)` パーミッション制御と組み合わせると「最低限のツールだけ許可しつつ Xcode MCP も動かす」という安全な構成が実現できます。
