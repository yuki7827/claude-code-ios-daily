---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-05-07"
date: 2026-05-07 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Claude Code v2.1.129 (May 6 リリース)](https://code.claude.com/docs/en/changelog) — `--plugin-url <url>` フラグを追加。URL から `.zip` プラグインをセッションに直接読み込めるようになり、iOS チーム内でカスタムプラグインを URL 配布するだけで全員が即座に導入可能に。`CLAUDE_CODE_PACKAGE_MANAGER_AUTO_UPDATE` で Homebrew インストール版のバックグラウンド自動更新も設定可能。1 時間プロンプトキャッシュ TTL が 5 分に無断ダウングレードされるバグも修正され、大規模 iOS プロジェクトでのコスト節約精度が向上。
- [Claude Code v2.1.131 (May 6 リリース)](https://code.claude.com/docs/en/changelog) — VS Code 拡張が Windows 環境で起動失敗するバグと、Mantle エンドポイント認証の `x-api-key` ヘッダ欠落を修正。

## 🛠 GitHub の動き

- [AvdLee/SwiftUI-Agent-Skill v3.1.0](https://github.com/AvdLee/SwiftUI-Agent-Skill) (⭐2.8k、週間インストール 16.6k) — Antoine van der Lee による SwiftUI ベストプラクティス集が 4/29 に v3.1.0 へ更新。iOS 26+ Liquid Glass エフェクトとフォールバックパターン（`.glassEffect()`・`GlassEffectContainer`）を追加。`/plugin install swiftui-expert@swiftui-expert-skill` 一発で Claude Code に導入可能。
- [haider-nawaz/liquid-glass-skill](https://github.com/haider-nawaz/liquid-glass-skill) — SwiftUI Liquid Glass (iOS 26+/macOS 26+) の構築と既存アプリ移行に特化した Claude Code スキル。5 フェーズ移行ワークフローと最新 Glass API の正確な使い方を内包。`/plugin marketplace add haider-nawaz/liquid-glass-skill` で追加可能。
- [kylehughes/apple-platform-build-tools-claude-code-plugin](https://github.com/kylehughes/apple-platform-build-tools-claude-code-plugin) (⭐57、v2.0.0) — ビルド・テスト・アーカイブ・デバイスデプロイ・コード署名を専用サブエージェント `builder` に委任するプラグイン。メインのコンテキストを汚さずに複雑なビルド操作を分離できる構成が特徴。

## 📝 日本語コミュニティ

- [Claude Codeで5日でiOSアプリをApp Storeに出した全記録 — 何を任せて何を任せちゃダメか](https://zenn.dev/seeda_yuto/articles/claude-code-ios-app-5days) (seeda_yuto, Zenn) — VoltKeep（Tesla 走行ログ記録、AES-256-GCM 暗号化、CloudKit 連携）を SwiftUI + CryptoKit + MapKit で 5 日間・55 コミット・約 4,000 行で完成させた実録。「Claude Code に何を任せてはいけないか」の正直な考察も含む。
- [Apple のサポートアプリに claude.md が同梱されて流出した話](https://qiita.com/yurukusa/items/9325e55da34010fa40de) (yurukusa, Qiita) — Apple Support v5.13 パッケージに claude.md が混入し、内部 AI チャット機能「Juno AI」の設計仕様が一時公開された事案。Apple が Claude Code を iOS アプリ開発に実際に採用していることが外部から初めて確認できた事例として注目。

## 🌐 海外コミュニティ / Tips

- [Claude Code vs Codex 2026 — What 500+ Reddit Developers Really Think](https://dev.to/_46ea277e677b888e0cd13/claude-code-vs-codex-2026-what-500-reddit-developers-really-think-31pb) (DEV Community) — SwiftUI オンリープロジェクトでは Claude Code が 60% の開発時間短縮を実現する一方、UIKit 混在では 35〜40% 止まり。Interface Builder/Storyboard は Claude に不透明なため、XcodeBuildMCP との組み合わせが事実上の前提と結論付け。
- [vabole/apple-skills](https://github.com/vabole/apple-skills) (⭐225) — iOS 26+ の最新 API（SwiftUI、Swift Concurrency、SwiftData、HealthKit、StoreKit、Human Interface Guidelines、Liquid Glass）を網羅した Apple 開発スキル集。`@Observable` マクロや `NavigationStack` など現代的なパターンのみ収録し、レガシー API を意図的に除外。

## 💡 今日のおすすめ実践 Tip

**`--plugin-url` で iOS チーム共有プラグインを URL 一本で配布する**

v2.1.129 で追加された `--plugin-url <url>` フラグを使うと、`.zip` にパッケージした iOS プロジェクト共通プラグイン（SwiftLint ルール・XcodeBuildMCP 設定・CLAUDE.md テンプレート等）を URL 指定でセッションに読み込めます。GitHub Releases にプラグインをアップロードし `claude --plugin-url https://github.com/<org>/<repo>/releases/latest/download/plugin.zip` とするだけで、チーム全員が同一設定を使えます。さらに `CLAUDE_CODE_PACKAGE_MANAGER_AUTO_UPDATE` を設定しておくと Homebrew 版 claude が自動アップグレードされ、手動の `brew upgrade` 忘れによるバージョン差異を解消できます。
