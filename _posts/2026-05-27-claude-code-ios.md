---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-05-27"
date: 2026-05-27 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

本日時点で新規リリースなし（最新は v2.1.150 / 5月23日）。

## 🛠 GitHub の動き

- [Do you really need an MCP to build your native app?](https://blog.sentry.io/do-you-really-need-an-mcp-to-build-your-app/) (Sentry Engineering) — XcodeBuildMCP 買収後に Sentry が公開したベンチマーク調査。**1,350 試行**で XcodeBuildMCP・素のシェル・`AGENTS.md`（CLAUDE.md 相当）のみの 3 アプローチを比較。成功率はいずれも 99.5%+ でほぼ同等だったが、コストは `AGENTS.md` が最安（MCP のツールスキーマオーバーヘッドがない）。一方 XcodeBuildMCP はスキーム発見の試行回数を 2.56 → 1.25 回に削減し、スクリーンショット・ビュー検査・ブレークポイントを使う多ターン UI 検証ループでのみ本領発揮。「単純ビルドなら CLAUDE.md + シェルで十分、複雑な UI 検証には MCP が不可欠」という実証結果。

- [Piebald-AI/claude-code-system-prompts](https://github.com/Piebald-AI/claude-code-system-prompts) — Claude Code の全システムプロンプト・27 ツール定義・Plan/Explore/Task サブエージェントプロンプトをバージョンごとに収集したリポ（v2.0.14 以降 187 バージョン分の差分 CHANGELOG 付き、リリース後数分で更新）。xcrun mcpbridge や XcodeBuildMCP のツール定義がシステムプロンプトにどう組み込まれているかを確認でき、iOS プロジェクトの CLAUDE.md を最適化する際の参考になる。

## 📝 日本語コミュニティ

- [Claude Codeで5日でiOSアプリをApp Storeに出した全記録 — 何を任せて何を任せちゃダメか](https://zenn.dev/seeda_yuto/articles/claude-code-ios-app-5days) (seeda_yuto, Zenn) — Tesla 走行ログアプリ（VoltKeep / SwiftUI + CryptoKit + MapKit）を 5 日・55 コミット・33 ファイルで App Store リリースした記録。自身で書いた Swift は 0 行だが、パフォーマンス問題の発見・アーキテクチャ判断は人間が担い、lazy loading・ディスクキャッシュ・並列フェッチの実装を Claude Code に委ねたフロー分担を詳述。30 万件データ消失インシデントも正直に記載しており、「任せてよいこと・いけないこと」の境界線が具体的にわかる。

- [Claude CodeからXcodeが提供するMCPに接続する方法](https://zenn.dev/pepabo/articles/4c0d1019ac1f7d) (GMO Pepabo, Zenn) — `claude mcp add --transport stdio xcode -- xcrun mcpbridge` で Apple 公式 MCP ブリッジを Claude Code に登録する手順を解説。XcodeWrite/XcodeRM を使うとファイル追加・削除が自動で `.xcodeproj` に反映されビルドターゲットとして即座に認識される点が特徴。minne iOS（MVVM + Figma Context MCP 連携）での活用方針も紹介。

- [タップルのネイティブQA戦略](https://developers.cyberagent.co.jp/blog/archives/63257/) (CyberAgent Developers Blog) — AI コーディングエージェント普及後に急増したリグレッションへの QA 対応実録。Claude Code・Devin でコード生成速度が向上した一方、週 216 項目のテストケースを 5 人体制で実施することが限界に。**Maestro（E2E 自動化）× Devin（テスト生成）× Playbook** で iOS 79 項目（37%）を自動化し Hotfix 件数を削減。AI 開発加速後の品質保証設計として参考になる。

- [XcodeBuildMCP×Claude Codeスキルシステムで、iOSビルドを自動化する](https://zenn.dev/kyoichi/articles/claude-code-xcodebuildmcp-ios-build) (kyoichi, Zenn) — XcodeBuildMCP と Claude Code スキルを組み合わせてビルド・テストループを自動化する実践ガイド。Apple 公式 MCP（mcpbridge）と XcodeBuildMCP の役割分担（ファイル操作・診断 vs. ビルド・テスト・シミュレータ）を整理し、コンテキスト消費を抑えながら自律ビルドを回す構成を解説。（[前回紹介](https://zenn.dev/kyoichi/articles/claude-code-plugin-xcode-mcp-hybrid)のハイブリッドプラグイン記事の続編）

## 🌐 海外コミュニティ / Tips

- [Two MCP Servers Made Claude Code an iOS Build System](https://blakecrosley.com/blog/xcode-mcp-claude-code) (Blake Crosley) — XcodeBuildMCP（82 ツール）と Apple 公式 Xcode MCP（20 ツール）の 2 本立てで Claude Code を iOS ビルドシステム化する実践記。XcodeBuildMCP がビルド・テスト・シミュレータ・LLDB を、Apple MCP がファイル操作・診断・SwiftUI プレビューを担い、非構造化ビルドログのパースを排除して構造化 JSON レスポンスに置き換える構成を MCP 追加 2 コマンドで実現する手順を解説。

- [LINE iOSアプリ開発を高速化するClaude Code基盤の設計思想](https://techblog.lycorp.co.jp/ja/20260119a) (LY Corporation Tech Blog) — LINE iOS チームが構築した Claude Code 活用基盤を公開した記事。「コンテキストは公共財」というコンセプトのもと 150 行の CLAUDE.md・パッシブ/アクティブに分けたエージェントスキル・サブエージェントへのビルドログ分離の 3 本柱で構成。Serena MCP によるシンボル参照精度向上も紹介。大規模 iOS コードベースへの Claude Code 導入設計の参考モデル。

## 💡 今日のおすすめ実践 Tip

**Sentry のベンチマークから逆算する「CLAUDE.md だけで解決できる範囲」の見極め方**

1,350 試行の実験から導き出された iOS 開発での MCP 活用判断基準：

- **CLAUDE.md で十分な用途**: CI/CD ビルド・単発テスト実行・コードレビュー補助。スキーム名・シミュレータ ID・ビルドコマンドを明記するだけでエージェントの試行錯誤を大幅削減。

- **XcodeBuildMCP が不可欠な用途**: SwiftUI レイアウト確認・クラッシュのバックトレース解析・アクセシビリティツリー操作を伴う多ターンの自律デバッグループ。

```markdown
# CLAUDE.md 最小有効構成（Sentry の実験から）

## ビルド設定
- スキーム: MyApp
- シミュレータ: iPhone 16 Pro (iOS 18.4)
- ビルドコマンド:
  xcodebuild -scheme MyApp \
    -destination 'platform=iOS Simulator,name=iPhone 16 Pro' build

## ファイル操作ルール
Xcode プロジェクトへのファイル追加・削除は XcodeWrite/XcodeRM を使うこと
（標準の Write/Bash での追加は .xcodeproj に反映されない）
```

この構成で「発見フェーズ」の無駄な試行を減らし、視覚確認が必要な場面のみ XcodeBuildMCP を使う構成がコスト最適。
