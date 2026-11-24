---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-11-25"
date: 2026-11-25 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート
- [Claude Code v2.1.331（2026-11-23）](https://www.havoptic.com/r/claude-code-2.1.331) — **Code Mode が Amazon Bedrock・Google Cloud・Microsoft Foundry・AWS 上の Claude Platform のターミナルセッションに対応拡大**。iOS CI を Bedrock や Vertex 上で動かしているチームも Code Mode の恩恵を受けられるように。また `claude doctor` に API 疎通確認機能が追加され、HTTP ステータスやエラー内容を表示できるようになった（MCP 接続トラブルの初動確認に便利）。

## 🛠 GitHub の動き
- 該当なし（本日は iOS 関連の新規 Issue / PR を確認できず）

## 📝 日本語コミュニティ
- [Xcode 同梱の Apple 公式スキル: 評価と使いどころ（Qiita / BlueEventHorizon）](https://qiita.com/BlueEventHorizon/items/e2874980d3c9c416adc5) — Xcode 27.2 + Claude Code で Apple 公式スキル 7 本を実評価した記事（2026-11-20）。**有用度「高」**: `device-interaction`（シミュレーター操作・スクリーンショット）と `swiftui-specialist`（SwiftUI の書き方・一覧画面の性能レビュー）。**有用度「中」**: `xctrace`（Instruments 記録を AI が読んで性能ボトルネックを特定）・`modernize-tests`（XCTest → Swift Testing 移行）・`audit-xcode-security-settings`。公式スキルとプロジェクト独自規約が衝突した場合は公式スキル優先で規約側を修正する運用が勧められている。
- [Muse × Claude Code で、絵を描かずに動くアバターを iOS アプリに載せた（Zenn / stak22）](https://zenn.dev/stak22/articles/4eae6e28ba5f29) — 画像生成 AI（Muse）が原画を生成し、Claude Code がリグ作成と書き出しを担って WKWebView + SwiftUI 構成の iOS アプリに動くアバターを実装した実践録（2026-11-03）。WKWebView を独自スキームで閉じた形で動かし外部通信を完全遮断・データ保存なしにする設計、表示していない間は描画ループを止めて約 20fps に抑えるネイティブ側のパフォーマンス制御、AI 生成差分をマスク外画素の変化有無で機械的に検証する手法が参考になる。

## 🌐 海外コミュニティ / Tips
- [Letting Claude Code Drive Xcode: Setting Up the Xcode MCP Server（MartianCraft / Cory Bohon）](https://martiancraft.com/blog/2026/11/xcode-mcp-server-setup-guide/) — Claude Code が `xcrun mcpbridge` 経由で起動中の Xcode に接続する仕組みと設定手順を詳解（2026-11-04）。`claude mcp add --transport stdio xcode -- xcrun mcpbridge` の 1 コマンドで登録し、Xcode の Intelligence 設定で外部エージェントを許可するだけ。ビルド・テスト結果をエラー単位で構造化取得できるためログ全体を渡す必要がなくトークンを節約できる点、SwiftUI プレビューを画像として取得してレイアウト確認しながら修正できる点が実務上のメリットとして挙げられている。複数 Xcode を同時起動している場合は `MCP_XCODE_PID` 環境変数で対象を指定する。
- [Claude Agent in Xcode 27: Setup and Real-iPhone Testing（TestMu AI）](https://www.testmuai.com/blog/claude-agent-xcode-real-device-testing/) — Claude Agent が自分で書いたコードのビルド・テスト判定まで行ってしまう問題を指摘し、**エージェント外からの独立した実機検証**を組み込んだワークフローを提案（2026-11-09）。推奨フロー：計画（`/plan`）→ ビルド＆ローカル確認 → Kane CLI（自然言語テスト仕様をクラウド実機で実行し終了コードで CI 判定）または XCUITest（複数機種並列） → 実機合格時のみマージ。エージェントが書いていない独立チェックを CI に挟むことで品質を担保する考え方は、MobileBuildMCP 活用時にも応用できる。
- [Apple's Xcode Plugin for Claude Code: What It Installs（blakecrosley.com）](https://blakecrosley.com/blog/xcode-plugin-claude-code) — 2026-11-06 に Anthropic 公式マーケットプレイスに追加された Apple 製 `xcode` プラグインの内部構造を分析（2026-11-11）。実体は約 15 行の JSON エントリで、インストール時に Mac 上の `xcrun agent plugin path --plugin-format claude` を実行してローカルにプラグインディレクトリを生成する仕組み。Xcode 27.2 環境ではスキル 16 個と MCP サーバー 1 個（`xcrun mcpbridge` 起動）が含まれ、セッションごとに約 4,463 トークンが追加される。手動で `xcrun mcpbridge` を設定済みの場合は MCP サーバーが重複抑制されスキルのみ追加される。**Xcode 更新のたびに内容が変わるためバージョン固定不可**という注意点も重要。

## 💡 今日のおすすめ実践 Tip

**`xctrace` スキルで Instruments 記録を AI に読ませてパフォーマンスボトルネックを特定する**

BlueEventHorizon の評価記事によると、Xcode 同梱の `xctrace` スキルは有用度「中」ながら実務で使えるとされている。
使い方は `xcrun agent skills export` でスキルをエクスポートし `.claude/skills/` に配置した上で、
Claude Code に「Instruments のトレースを読んでボトルネックを特定して」と指示するだけ。
手動で .trace ファイルを開いて読む作業が不要になり、CPU／メモリのホットスポットを言語化してもらえる。
評価時は `xcode-select` で フル Xcode を向けておくことが前提条件。
