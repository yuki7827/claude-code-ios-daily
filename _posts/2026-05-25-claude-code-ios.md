---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-05-25"
date: 2026-05-25 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Claude Code v2.1.149 Changelog](https://code.claude.com/docs/en/changelog) — `/usage` コマンドがカテゴリ別使用量内訳（スキル・サブエージェント・プラグイン・MCPサーバーごと）を表示するよう強化。iOS ビルドで XcodeBuildMCP を多用する際の消費量把握に直結する改善。同バージョンで `/diff` 詳細ビューがキーボード操作（矢印キー・j/k・PgUp/PgDn）に対応し、Xcode プロジェクトのコード差分確認がスムーズになった。

## 🛠 GitHub の動き

- [jamesrochabrun/ClaudeCodeSDK](https://github.com/jamesrochabrun/ClaudeCodeSDK) — Swift から Claude Code をプログラム制御する SDK。v2.0 で Agent SDK バックエンドに対応し「2〜10 倍高速化」を実現。Process API の制限から iOS ターゲットは廃止・macOS 13+ 専用になったが、macOS アプリに Claude Code を組み込んで自律コーディング支援を実装したい開発者に最短ルート。付属の [ClaudeCodeUI](https://github.com/jamesrochabrun/ClaudeCodeUI) でデスクトップ UI も構築可能。

- [kylehughes/apple-platform-build-tools-claude-code-plugin](https://github.com/kylehughes/apple-platform-build-tools-claude-code-plugin) — `xcodebuild`・SPM・`simctl`・`devicectl`・コード署名・IPA エクスポートをまとめてカバーする Claude Code プラグイン（v2.0.0 / 2026-03-08）。Agent Skill とサブエージェントが連携し、プロジェクト構造を自動検出してビルド〜テスト〜配布までを自律実行する。

- [keskinonur/claude-code-ios-dev-guide](https://github.com/keskinonur/claude-code-ios-dev-guide) — XcodeBuildMCP・PRD 駆動ワークフロー・ultrathink（拡張思考）・Plan Mode を組み合わせた Swift/SwiftUI 開発のセットアップガイド。17 セクション構成で CLAUDE.md テンプレート・カスタムスラッシュコマンド・Sandbox Mode の権限設計まで網羅。

## 📝 日本語コミュニティ

- [Xcode MCP×Claude Codeプラグインで、iOSビルドを自動化する](https://zenn.dev/kyoichi/articles/claude-code-plugin-xcode-mcp-hybrid) — Apple 純正 Xcode MCP（`xcrun mcpbridge`）と XcodeBuildMCP を併用するハイブリッド構成を解説。それぞれの役割分担（ファイル操作・診断 vs. ビルド・テスト・シミュレータ管理）を整理した実践記事。

- [Xcode 26.3のMCPとは何か — 7つのSkillsで実践する](https://zenn.dev/dsgarage/articles/xcode-263-mcp-skills) — Xcode 26.3 に搭載された MCP の仕組みを 7 つのスキル構成で体験するハンズオン形式の記事。iOS 開発者が Claude Agent を Xcode から使い始める際の入門コンテンツ。

- [Xcoded & Claude AgentはSwift Assitantから何が出来るようになったのか](https://zenn.dev/yokot/articles/0355efbf2588a4) — Xcode 26 の Swift Assist との差分を整理し、Xcode 26.3 の Claude Agent で「何が新しくできるのか」を比較した記事。自律ビルド・テスト実行・SwiftUI プレビュー自動キャプチャの 3 点が主な進化として挙げられている。

- [Claude Code で5時間で2つのミニアプリを作った話](https://zenn.dev/hololab/articles/9b8eb0570975ac) — 5 時間で 2 本の iOS ミニアプリを Claude Code で完成させた体験記。詰まりやすいポイントと Claude Code への渡し方の工夫が具体的にまとまっている。

## 🌐 海外コミュニティ / Tips

- [Reduce iOS Development Time by 60% with Claude Code](https://medium.com/@osmandemiroz/reduce-ios-development-time-by-60-with-claude-code-86a4e9d864ca) — XcodeBuildMCP と CLAUDE.md に iOS バージョン・Swift バージョンを明示することで不要なエラーループを削減し、開発速度を 60% 向上させた手法を紹介。「`.pbxproj` ファイルを AI に触らせない」ベストプラクティスも詳述。

- [Claude Code Weekly Update (2026/05/23 週)](https://qiita.com/saitoko/items/007cee379d62c2d59b60) — 週次まとめ Qiita 記事。7 月 13 日まで週次利用上限が 50% 増量、macOS UI 操作向け Computer Use の提供、`/skills` コマンドによるマーケットプレイス即時検索が注目点。

## 💡 今日のおすすめ実践 Tip

**`/usage` でMCP・サブエージェントのコストを可視化する**

v2.1.149 から `/usage` がカテゴリ別の消費量を表示するようになった。XcodeBuildMCP を使った iOS 開発セッションでは、ビルド 1 回あたりに MCP ツール呼び出しが数十回走ることがある。`/usage` でサブエージェントと MCP サーバーの消費量を確認し、特定の操作（例：毎回フルビルドしている箇所）が突出していないかチェックすることで、増量された週次上限（7/13 まで）を賢く使い切ることができる。
