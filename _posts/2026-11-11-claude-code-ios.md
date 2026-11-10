---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-11-11"
date: 2026-11-11 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- 該当なし（直近の新規リリースは v2.1.319・2026-11-06 で、変更点は 11-07 付け記事に記載済み。公式 changelog で次バージョンを継続確認中）

## 🛠 GitHub の動き

- [tarunyadav1/ios-app-lifecycle](https://github.com/tarunyadav1/ios-app-lifecycle) — Claude Code & Codex 向け iOS 開発フルライフサイクルプラグイン。16 種の SKILL.md（SwiftUI / Liquid Glass / App Intents / TestFlight / ASO / クラッシュ監視など）＋ XcodeBuildMCP・Xcode MCP bridge・RevenueCat・Mobbin の 4 MCP サーバーを同梱。App Review 提出・価格変更など破壊的操作は必ずユーザー確認を挟む安全設計。インストールは `/plugin marketplace add tarunyadav1/ios-app-lifecycle` → `/plugin install ios-app-lifecycle`。
- [keskinonur/claude-code-ios-dev-guide](https://github.com/keskinonur/claude-code-ios-dev-guide) — Swift/SwiftUI 向け PRD 駆動ワークフローと `ultrathink`（拡張思考）を組み込んだ Claude Code 設定ガイド。`docs/PRD.md → docs/specs/ → docs/tasks/` のドキュメント構造とプランモード運用の CLAUDE.md テンプレートが整理されており、iOS 17+ プロジェクトをゼロから立ち上げる際の出発点として参照できる。

## 📝 日本語コミュニティ

- [Muse × Claude Code で、絵を描かずに動くアバターを iOS アプリに載せた（Zenn / stak22・2026-11-03）](https://zenn.dev/stak22/articles/4eae6e28ba5f29) — 画像生成 AI「Muse」でキャラ原画と表情差分を生成し、Claude Code が Mesh Avatar Studio でリグ組みと書き出しを担当、WKWebView に同梱ファイルのみ読み込む形で iOS アプリに組み込んだ実装レポート。外部通信なし・isUserInteractionEnabled = false で操作は SwiftUI 側に統一する設計など、非エンジニアが Claude Code を介してアバター機能を追加するフローが実例付きで説明されている。

## 🌐 海外コミュニティ / Tips

- [CLAUDE.md for Swift and iOS: 13 Rules That Stop AI From Writing Unsafe, Non-Idiomatic Apple Code（dev.to / olivia_craft）](https://dev.to/olivia_craft/claudemd-for-swift-and-ios-13-rules-that-stop-ai-from-writing-unsafe-non-idiomatic-apple-code-4nld) — `Package.swift` の横に置く CLAUDE.md に書くべき 13 ルール（`weak self` 必須、フォースアンラップ禁止、UI は @MainActor、async/await 優先など）をまとめた記事。AI が「コンパイルは通るが iOS 的に危険なコード」を出力するのを防ぐための具体的な文言が示されており、既存プロジェクトへの追記にそのまま使える形式になっている。
- [How I Shipped a Production iOS App Solo with Claude（dev.to / sahil_kat）](https://dev.to/sahil_kat/how-i-shipped-a-production-ios-app-solo-with-claude-26l3) — 2014 年から iOS を離れていた開発者が 2026 年にソロで App Store アプリ「BaselineBody」をリリースした実践録。ポイントは「コード生成器」ではなく「構造化ペアプログラマー」として扱うこと。機能実装前に platform API gap audit を走らせる、主要サブシステム着手前に framework pre-flight check で後から変えにくい設計判断を洗い出す、というワークフローが再現性の高い形で整理されている。

## 💡 今日のおすすめ実践 Tip

**CLAUDE.md に「Warnings are errors」を明記する**

dev.to の olivia_craft 記事にある 13 ルールの中で特に即効性が高いのが「`-warnings-as-errors` を有効にした上でビルドすること」の明記。Claude Code はデフォルトでビルドが通れば OK と判断しがちだが、CLAUDE.md に `All build warnings must be resolved before marking a task done. Treat warnings as errors.` と 1 行加えるだけで、Swift Concurrency 警告や非推奨 API 警告を放置したまま「完了」とされる問題を防げる。既存プロジェクトに追記する場合は `Package.swift` の `swiftSettings` に `.unsafeFlags(["-warnings-as-errors"])` を追加する手順も Claude に指示しておくと一貫性が保たれる。
