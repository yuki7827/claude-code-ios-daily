---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-08-20"
date: 2026-08-20 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- **v2.1.235（2026-08-18）リリース**（[Claude Code 変更ログ](https://code.claude.com/docs/en/changelog)）— v2.1.234（2026-08-17）の次のリリース。iOS 開発観点での主な変更点: ① **オプションのスペルチェック設定を追加** — プロンプト入力欄でタイプ中にスペルミスに下線が引かれる機能。`aspell`・`hunspell`・`ispell` のいずれかがインストールされていれば動作する。CLAUDE.md 作成時や英語混じりのプロンプト入力の精度向上に役立つ; ② **全体プロンプトキャッシュの無効化バグを修正** — 長いセッション中にキャッシュが不正に無効化される問題が修正され、Xcode ビルドログ等の大きなコンテキストを扱うセッションの安定性が向上; ③ **Vim モードの改善** — 詳細トランスクリプト（Ctrl+O）切り替えやパネルクローズ時に NORMAL モードとカーソル位置が保持されるようになった。ヤンクレジスタがダイアログ・履歴検索・トランスクリプトビューを跨いで保持されるようになり、空プロンプトに戻るアンドゥの動作も改善; ④ **パーミッションダイアログとコンテキスト上限エラーメッセージを改善** — 上限に達した際のメッセージがより明確になり、次の操作が判断しやすくなった。

## 🛠 GitHub の動き

- [Issue #88006 — iOS Simulator MCP: `control action:"launch"` でアプリに launch arguments を渡す方法がない（`text` パラメータがサイレントに無視される）（OPEN / 2026-08-19）](https://github.com/anthropics/claude-code/issues/88006) — iOS Simulator MCP ツールで `control action:"launch"` を実行すると内部では `xcrun simctl launch` を呼ぶだけで、`text` パラメータとして渡した launch arguments が完全に無視される問題。`-FIRDebugEnabled`・`-UITESTING` などのデバッグフラグや環境変数をアプリ起動時に渡す手段が現状存在しない。UI テスト・Debug ビルドの自動化を Claude Code に任せるワークフローで影響を受ける。**現時点の回避策は XcodeBuildMCP の `launch` ツールを使うこと**（XcodeBuildMCP は `xcrun simctl launch` の引数を完全サポート）。

- [Issue #87875 — iOS Simulator パネルへのブラウザパネルの annotate（鉛筆）ツール追加リクエスト（OPEN / 2026-08-19）](https://github.com/anthropics/claude-code/issues/87875) — ブラウザペインで利用できるアノテーション（鉛筆・描画）ツールを iOS Simulator パネルでも使えるようにしてほしいというフィーチャーリクエスト。Simulator パネルのスクリーンショットに UI フィードバック（「この余白が足りない」「ボタンが被っている」等）を書き込んでエージェントに渡せると UI 修正サイクルが短縮できる。現時点では未対応。

## 📝 日本語コミュニティ

- 該当なし（2026-08-19〜20 の範囲で iOS / Swift / Xcode に特化した新規記事は確認できず）

## 🌐 海外コミュニティ / Tips

- [Two MCP Servers Made Claude Code an iOS Build System（blakecrosley.com）](https://blakecrosley.com/blog/xcode-mcp-claude-code) — XcodeBuildMCP と Apple 公式 Xcode MCP を組み合わせることで Claude Code が「Web 開発と同等の AI ビルドシステム」として機能するようになるという実践レポート。XcodeBuildMCP（v2.3.2 / 82 ツール）がビルド・テスト・シミュレータ・LLDB デバッグをカバーし、Xcode MCP（20 ツール）がファイル操作・診断・SwiftUI Previews のキャプチャを担う二本柱の構成。両者を組み合わせることで Claude がビルドログを構造化 JSON で受け取り、エラー箇所を正確に特定しながらビルド→修正→プレビュー確認のループを自律的に回せるようになる。全投稿での初掲載。

## 💡 今日のおすすめ実践 Tip

**「iOS Simulator MCP で launch arguments が渡せない問題（#88006）の回避策 — XcodeBuildMCP の `launch` ツールを使う」**

Issue #88006 で報告された通り、Claude Code Desktop の iOS Simulator パネル（`control action:"launch"`）ではアプリ起動時に launch arguments を渡す手段がありません。`-UITESTING`・`-FIRDebugEnabled` などのフラグが必要な UI テスト自動化や Debug ビルドのワークフローで詰まります。

**CLAUDE.md への追記例（回避策）**

```markdown
## iOS シミュレータへのアプリ起動方法

iOS Simulator パネルの `control action:"launch"` は launch arguments を無視するバグがあります（Issue #88006）。
launch arguments が必要な場合は必ず XcodeBuildMCP の launch ツールを使ってください。

launch arguments が必要な主なケース:
- UI テスト実行時（`-UITESTING 1` でテストモードを有効化）
- Firebase デバッグログ（`-FIRDebugEnabled`）
- 特定の環境変数を渡してスタブ API サーバーを向かせる場合
```

**Claude Code への依頼例**

```
「XcodeBuildMCP の launch ツールで MyApp を iPhone 16 Simulator に起動してください。
起動引数として -UITESTING 1 -MyAPIBaseURL http://localhost:8080 を渡してください。」
```

内部的に実行されるコマンド:

```bash
xcrun simctl launch <device-uuid> com.example.MyApp -UITESTING 1 -MyAPIBaseURL http://localhost:8080
```

この方法なら `XCUIApplication().launchArguments` でテストコードから引数を読み取れる通常の XCTest フローと完全に互換性があります。Issue #88006 が修正されるまでは、CLAUDE.md に XcodeBuildMCP の launch ツールを優先することを明示しておくと、エージェントが自律的に正しい手段を選択するようになります。
