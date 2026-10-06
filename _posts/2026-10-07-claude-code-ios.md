---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-10-07"
date: 2026-10-07 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- **[v2.1.292（2026-10-06）リリース](https://code.claude.com/docs/en/changelog)** — **Agent ツールに `effort` パラメータを追加**。サブエージェントを指定した effort レベルで起動できるようになった。iOS 並列開発ワークフローでビルドエージェントとテストエージェントの計算コストを個別に制御する際に有効。また **`claude plugin install` に `--marketplace <source>` フラグを追加**し、マーケットプレイスの登録とインストールを一発で行えるように。**`$.model.complete` にプロンプトキャッシュ対応（`cache: true`）**が加わり、iOS 専用 mod の応答速度向上が見込める。セキュリティ修正として `permissionMode: auto` のサブエージェントが利用不可環境で誤って auto モードに入るバグを修正（Remote Control 経由の iOS CI 自動化の信頼性向上）、`/ultrareview` アップロードのステージングファイルをサンドボックスコマンドが読めていた問題を修正。MCP ツール名が 128 文字超でリクエスト失敗する問題も修正。

- **[v2.1.291（2026-10-06）リリース](https://code.claude.com/docs/en/changelog)** — **クラウドセッションで権限プロンプトへの回答がドロップする（v2.1.290 リグレッション）を修正**。iOS CI/CD でクラウドセッションを使っている環境では即アップデート推奨。セッション終了時に最後のメッセージが消失するバグ（v2.1.288 リグレッション）も同時修正。

- **[v2.1.290（2026-10-05）リリース](https://code.claude.com/docs/en/changelog)** — **`claude attach <name>` / `claude logs <name>` でセッション名の部分一致から素早くアタッチ・ログ確認できるショートカットを追加**。長時間の iOS ビルドセッションへの再アタッチが大幅に楽になる。`/claude-api managed-agents-onboard` コマンドで Managed Agents のセットアップが可能に。WebFetch が 100,000 文字以降をサイレントに切り捨てていたバグも修正（ドキュメント参照系タスクの精度向上）。

## 🛠 GitHub の動き

- **[Issue #80205（2026-07-22 作成 / 2026-10-05 CLOSED）— iOS Simulator MCP: `control { action: "launch" }` が "disclaimer exited with code 143" で失敗 — sidecar sandbox が `build` で生成したアプリの読み込みを拒否](https://github.com/anthropics/claude-code/issues/80205)** — iOS Simulator の MCP ツールでアプリをビルド後に `launch` アクションを実行すると、sidecar のサンドボックスプロファイルがビルドアーティファクトへのアクセスを拒否し code 143 で終了する問題が 2026-10-05 にクローズ（修正済み）。ビルド → 起動 → UI テストのループを自動化しているワークフローで発生しやすかった。ラベル: `area:desktop`。

- **[Issue #81520（2026-07-27 作成 / 2026-10-05 CLOSED）— Desktop iOS Simulator パネルが macOS 27 beta でクラッシュループ（BitmapStream Metal/CoreImage abort）— Xcode 27 beta 4 で SimulatorKit のパスが変更](https://github.com/anthropics/claude-code/issues/81520)** — macOS 27 beta / Xcode 27 beta 4 へのアップデート後に Claude Code デスクトップアプリの iOS Simulator パネルが起動直後にクラッシュし続ける問題が 2026-10-05 にクローズ。Xcode 27 beta 4 で `SimulatorKit.framework` が新パスに移動したことで BitmapStream の Metal/CoreImage パスが abort していた原因が修正済み。macOS 26 系で macOS 27 beta に既にアップデートしている開発者は最新版で正常動作することを確認推奨。

## 📝 日本語コミュニティ

- 該当なし（2026-10-07 時点で Zenn / Qiita に iOS 文脈の新規記事は確認できず）

## 🌐 海外コミュニティ / Tips

- **[Claude Code vs Codex: iOS Simulator build/test comparison（dev.to: stevengonsalvez）](https://dev.to/stevengonsalvez/claude-code-vs-codex-ios-simulator-build-test-that-settled-the-debate-41)** — Claude Code と GitHub Codex を同一の iOS プロジェクト（SwiftUI + XCTest）でビルド→シミュレーター起動→テスト実行のループを比較したベンチマーク記事。Claude Code は XcodeBuildMCP（82 ツール）と Apple 公式 Xcode MCP（`xcrun mcpbridge` の 20 ツール）の二重構成でツール呼び出しの粒度が細かく、コンパイルエラーの自己修正サイクルが速い点が評価されている。一方で macOS / Xcode のバージョン依存が強くセットアップコストが高い点も指摘あり。

- **[Apple Platform Build Tools Claude Code Plugin（GitHub: kylehughes/apple-platform-build-tools-claude-code-plugin）](https://cdn.jsdelivr.net/gh/kylehughes/apple-platform-build-tools-claude-code-plugin@main/README.md)** — `xcodebuild`・`swift build`・`swift test`・`xcrun simctl` の各コマンドを Claude Code のツール呼び出しとして統合するプラグイン。XcodeBuildMCP のフル導入が難しい環境（Xcode 旧バージョン、macOS 26 以前）向けの軽量代替として機能する。`claude plugin install kylehughes/apple-platform-build-tools-claude-code-plugin` で導入可能。

## 💡 今日のおすすめ実践 Tip

**v2.1.292 の `effort` パラメータで iOS ビルド／テストエージェントを分離する**

```json
// .claude/settings.json 側のエージェント設定例（概念）
{
  "agents": [
    { "name": "build", "effort": "low" },
    { "name": "test",  "effort": "high" }
  ]
}
```

v2.1.292 から Agent ツールに `effort` パラメータが追加された。iOS プロジェクトでは「ビルドエージェント（コンパイル確認のみ、高速・低コスト）」と「テストエージェント（XCTest / Maestro の詳細解析、高精度）」を異なる effort で並列起動することで、トークンコストを抑えつつ品質チェックを保てる。`agent.spawn` と組み合わせ、UI テスト専用エージェントだけ `effort: "high"` にするパターンが有効。
