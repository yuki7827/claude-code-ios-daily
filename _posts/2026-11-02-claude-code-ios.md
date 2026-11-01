---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-11-02"
date: 2026-11-02 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- 2026-11-02 時点で新規リリースなし。最新は v2.1.316（2026-10-31）。v2.1.316 の内容（`CLAUDE_CODE_DISABLE_ULTRATHINK` / `CLAUDE_CODE_SUBAGENT_EFFORT` 追加、Opus 4.8 fast mode 非推奨化など）は 11-01 キャッチアップを参照。

## 🛠 GitHub の動き

- [#106251（2026-10-30 open）Simulator パネルのバックグラウンド Xcode チェックが macOS keychain に不明な admin パスワードプロンプトを出し keychain がハングする](https://github.com/anthropics/claude-code/issues/106251) — Claude Desktop の iOS Simulator パネルが起動時に行う Xcode インストール確認処理が、macOS keychain に対して admin パスワードを要求するダイアログを無説明に表示し、プロンプトを放置するとシステムの keychain サービスがハングする問題。XcodeBuildMCP や xcrun mcpbridge と組み合わせて使っている環境でも keychain ハングが波及するケースが報告されている。暫定回避策は Claude Desktop を Xcode インストール済み Mac で起動するか、Simulator パネルを使わない構成に切り替えること。ラベル: `bug`, `platform:macos`。

- [#104271（2026-10-22 open）Feature: iOS Simulator パネルでデバイスフレームを非表示にするオプション](https://github.com/anthropics/claude-code/issues/104271) — iOS Simulator パネルに表示されるスマートフォン外枠（デバイスフレーム）を非表示にする設定を求める機能要望。フレームを非表示にするとパネルの有効ピクセル面積が増え、スクリーンショット解析の精度向上が期待できる。また iPad Pro のように縦横比が正方形に近い端末でフレームがパネル内に収まらない問題の回避にもなる。現在 OPEN・トリアージ中。

## 📝 日本語コミュニティ

- [Figma MCP × Claude Code で、実装はどこまで自動化できるか（Qiita / shibuya-ys）](https://qiita.com/shibuya-ys/items/2c9e2cc58c7744c6d7b1) — Claude Code v2.1.215 と Figma MCP を組み合わせ、iOS 17+ / SwiftUI での UI 実装自動化を検証した記事。Figma のデザインデータをそのまま Claude Code に渡して SwiftUI コードを生成するフローを検証し、コンポーネント単位の単純な View は高精度で生成できる一方、複合アニメーションや条件付きレイアウトは人間のレビューが必要という結論。Figma Context MCP（figma-developer-mcp）の設定手順と、CLAUDE.md に iOS 17 以降の `@Observable` 構文を明示することでコード品質が上がる点も解説されている。

## 🌐 海外コミュニティ / Tips

- [How I Shipped a Production iOS App Solo with Claude（DEV Community / sahil_kat）](https://dev.to/sahil_kat/how-i-shipped-a-production-ios-app-solo-with-claude-26l3) — 一人で本番 iOS アプリをリリースした体験記。「Claude にアプリを書かせる」ではなく「Claude をペアプログラマーとして使う」という姿勢を一貫させた点が特徴。CLAUDE.md でアーキテクチャ決定を文書化し、機能実装後にコードレビュープロンプト（「このコードのバグ・パフォーマンス問題・Swift の慣用表現から外れている点を列挙せよ」）を挟む二段階ワークフローを採用。App Store 審査対応の注意事項（プライバシーポリシー URL・ATT ダイアログの文言）も Claude がチェックリストを生成して助けてくれたと報告している。

## 💡 今日のおすすめ実践 Tip

**Figma MCP + CLAUDE.md の組み合わせで SwiftUI の初期実装コストを下げる**

Figma MCP（[figma-developer-mcp](https://github.com/GLips/Figma-Context-MCP)）を Claude Code に接続すると、Figma のデザインデータ（色・タイポグラフィ・スペーシング・コンポーネント名）を MCP ツール経由で直接参照しながら SwiftUI を生成できるようになる。

効果を高めるために CLAUDE.md に以下を追記しておくのが定石だ：

```markdown
# デザインシステム規約
- iOS 最小バージョン: iOS 17+
- 状態管理: @Observable（ObservableObject は使わない）
- カラー: Assets.xcassets に定義済みの Color Set 名を使う（ハードコードしない）
- フォント: Dynamic Type 対応を必須とし UIFont.TextStyle を明示する
- spacing / padding: デザイントークン（4pt グリッド）に合わせる
Figma MCP から色・スペーシングを取得したときは、上記ルールに変換してから実装すること
```

これにより Claude は Figma の生データ（16進カラーコードや px 値）を自動的に iOS のデザイントークンに変換して出力するようになる。
複合アニメーションや条件付きレイアウトは自動生成後に必ず人手でレビューする必要があるが、単純なコンポーネントの初期実装は大幅に省力化できる。
