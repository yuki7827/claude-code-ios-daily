---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-10-09"
date: 2026-10-09 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- 該当なし（2026-10-09 時点で v2.1.293 が最新。前日キャッチアップ済み）

## 🛠 GitHub の動き

- **[Issue #100495（2026-10-08 作成 / OPEN）— [Bug] iOS Simulator でサンドボックステスター用 iCloud サインインを拒否する](https://github.com/anthropics/claude-code/issues/100495)** — Claude Code の iOS Simulator コントロールツールが、StoreKit / CloudKit のサンドボックステスターとして iCloud アカウントにサインインしようとする操作を拒否する問題。インタラクティブな認証フロー（Apple ID 入力 → 2FA）が外部通信を伴うためセキュリティポリシーに引っかかっているとみられる。StoreKit の課金フローや iCloud 同期をエンドツーエンドで自動テストしたい iOS 開発者に影響する。現在 OPEN でトリアージ中。ラベル: `enhancement`, `platform:macos`, `area:security`, `area:permissions`。

- **[Issue #98520（2026-09-30 作成 / 2026-10-07 CLOSED）— デスクトップアプリの iOS Simulator パネルが Mac クリップボードを共有しない（端末内にペーストできない）](https://github.com/anthropics/claude-code/issues/98520)** — Claude Code デスクトップアプリに組み込まれた iOS Simulator パネルで、Mac 側でコピーしたテキスト（テストデータ、アカウント情報、URLスキームなど）をシミュレーター内のテキストフィールドにペーストできなかった問題が 2026-10-07 にクローズ（修正済み）。`xcrun simctl pasteboard sync` 等の回避策が不要になる。ラベル: `bug`, `has repro`, `platform:macos`, `area:desktop`。

## 📝 日本語コミュニティ

- **[[SwiftUI] iOS 26 の Liquid Glass 対応を Claude に丸投げしたら、すけすけになりすぎた話（Zenn: rpaka）](https://zenn.dev/rpaka/articles/7c948f1f12e56c)** — iOS 26 で導入された Liquid Glass デザインシステムへの移行を Claude Code に任せたところ、過剰にガラス効果が適用されて UI が透け透けになった失敗談。自動的な一括置換は得意だが「どこに適用すべきか」の判断は人間が行う必要があるという教訓をまとめている。`GlassEffectContainer` を使った複数要素の管理方法についても言及あり。

- **[AI に iOS アプリを実装させるとき、失敗を種類で分ける（Qiita: yasu-ya、2026-10-06）](https://qiita.com/yasu-ya/items/989a098d54e9979b7588)** — GitHub Issue → Claude Code 実装 → ビルド/テスト/E2E 検証 → PR 作成の自動ループを iOS アプリで試した実践報告（続編）。失敗を「実装の問題」「テスト環境の問題」「モデル認証の問題」など 5 種類に分類し、AI にやり直しを指示する価値があるのは実装の問題のみと整理している。また未ログイン状態の Claude Code が終了コード 0 で正常終了したように見せかける挙動についても注意喚起。Xcode 26.6 / GitHub Actions / Claude Code で確認済み。

- **[Xcode 26.3 で MCP が追加され Claude Code から SwiftUI のレンダリングができるようになった（Zenn: zaico）](https://zenn.dev/zaico/articles/98a1528d07d195)** — Xcode 26.3 RC で追加された MCP ブリッジ（`xcrun mcpbridge`）を使って Claude Code のターミナルから SwiftUI のプレビューレンダリングを呼び出せるようになった仕組みを解説。`claude mcp add --transport stdio xcode -- xcrun mcpbridge` の 1 コマンドで接続でき、Claude Code からプレビュー生成・診断・ドキュメント検索が利用可能になる点を具体的なプロンプト例つきで紹介。

## 🌐 海外コミュニティ / Tips

- **[Agentic Coding in Xcode 26.3 with Claude Code and Codex（swiftjectivec.com）](https://swiftjectivec.com/Agentic-Coding-Codex-Claude-Code-in-Xcode/)** — Xcode 26.3 のエージェントコーディング機能で Claude（Anthropic）と Codex（OpenAI）を同一プロジェクトで比較したレポート。Claude はドキュメント検索と SwiftUI のコード生成が速く、Codex は既存コードのリファクタリング提案に強い傾向があると報告。どちらも IDE 内から直接 MCP ツールを呼べるが、外部 XcodeBuildMCP との併用が引き続き推奨されている。

- **[XcodeBuildMCP v2.7.0 — Xcode 27 の Device Hub シミュレーター対応を UI オートメーションツールに追加（GitHub: fastmcp-me/xcodebuildmcp）](https://github.com/fastmcp-me/xcodebuildmcp)** — 2026-09-19 リリースの v2.7.0 で Xcode 27 から iOS Simulator の管理が `Simulator.app` から `DeviceHub.app`（`com.apple.dt.Devices`）に移行した変更に追従。UI オートメーションツール（tap/swipe/screenshot）が新しいデバイスハブ経由のシミュレーターでも動作するようになった。Xcode 27 環境の CI 自動化を組んでいる場合は v2.7.0 以降へのアップデートが推奨。

## 💡 今日のおすすめ実践 Tip

**iCloud / StoreKit サンドボックステストを Claude Code で自動化する際の現実的な回避策**

Issue #100495 が示すように、Claude Code の iOS Simulator コントロールツールは現時点でインタラクティブな iCloud サインイン（サンドボックステスターアカウント）を直接操作できない。課金・購入フローを含む E2E テストを Claude Code に任せる場合、以下の段階的な手順が現実的：

```bash
# 1. シミュレーターを事前にサンドボックスアカウントでサインイン済みの状態に保存
xcrun simctl list devices | grep Booted
xcrun simctl io booted screenshot before_login.png

# 2. Claude Code にはサインイン済みシミュレーターを起動後に引き渡す
# CLAUDE.md に以下を追記して前提条件を明示する
```

```markdown
## テスト環境の前提
- iOS Simulator には事前にサンドボックステスター（sandbox_tester@example.com）でサインイン済みのこと
- Claude Code は iCloud 認証フローを自動操作できないため、サインインは手動で行うこと
- StoreKit テストは Xcode の StoreKit Configuration File（.storekit）を使いローカルモードで実行する
```

`StoreKit Configuration File` を使ったローカルモードにすれば iCloud 認証なしで課金フローをテストできるため、Claude Code による完全自動化が可能になる。本物のサンドボックス課金が必要な場合のみ手動サインインを挟む設計にしておくと、CI/CD パイプラインとの整合性も保ちやすい。
