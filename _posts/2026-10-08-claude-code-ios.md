---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-10-08"
date: 2026-10-08 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- **[v2.1.293（2026-10-07）リリース](https://code.claude.com/docs/en/changelog)** — **デフォルトの Haiku モデルが Claude Haiku 5.5（1M コンテキスト）に変更**。コスト重視のサブエージェント（ビルドログ解析・軽量コードレビュー等）が 1M トークンのコンテキストウィンドウを持つようになり、大型 iOS モノレポでも全コンテキストを渡せるケースが増える。**コンテキスト圧縮のアクション順序バグを修正** — 圧縮前に完了したはずのアクション（ビルド・テスト等）が完了済みとみなされず再実行または取り消されていた問題が解消。**HTTP MCP 接続のメモリリークを修正** — リクエストごとにメモリが蓄積し接続クローズまで解放されなかった問題で、XcodeBuildMCP や Xcode MCP（mcpbridge）を長時間セッションで使用する場合に影響していた可能性あり。バックグラウンドキー（←）送信でキューメッセージが消失するバグ、フッターのエージェント数表示バグ、vim モードのカーソル挙動バグ、Grep/Glob でパスを読めない際に「マッチなし」として誤報告されるバグもあわせて修正。

## 🛠 GitHub の動き

- **[Issue #95085（2026-09-17 作成 / 2026-09-29 CLOSED）— iOS Simulator control ツール：Xcode 27 で attach/boot がタイムアウト — 旧 Simulator.app を対象にしており DeviceHub.app（com.apple.dt.Devices）が認識されない](https://github.com/anthropics/claude-code/issues/95085)** — Xcode 27 から iOS Simulator の起動・管理が `Simulator.app` から `DeviceHub.app`（バンドル ID: `com.apple.dt.Devices`）に移行したことに伴い、Claude Code の iOS Simulator control ツールが旧パスを参照し続け `attach` / `boot` アクションが常にタイムアウトしていた問題が 2026-09-29 にクローズ（修正済み）。Xcode 27 環境で「iOS Simulator ツールが応答しない / タイムアウトが多発する」という症状があった場合は最新版への更新で解消する。ラベル: `bug`, `has repro`, `platform:macos`, `area:mcp`。

- **[Issue #96904（2026-09-24 作成 / 2026-10-02 CLOSED）— iOS Simulator パネルが Intel Mac でブラックスクリーン（ストリームは正常、ネイティブ Simulator は正常動作）](https://github.com/anthropics/claude-code/issues/96904)** — Intel Mac（macOS 26.x / Xcode 27 環境）で Claude Code デスクトップアプリの iOS Simulator パネルが常にブラックスクリーンになる問題が 2026-10-02 にクローズ。`xcrun simctl io booted screenshot` 等ネイティブコマンドでは正しい画面が取得でき、ストリーム自体は健全な状態にもかかわらずパネルだけが真っ黒になっていた。Apple Silicon Mac では発生せず Intel 固有だったことから、Metal / GPU パス周りの Intel 向けコードパスが修正された模様。ラベル: `bug`, `has repro`, `platform:macos`, `area:desktop`。

- **[Issue #70144（2026-06-22 作成 / 2026-08-17 CLOSED / 2026-10-06 コメント追加）— [iPadOS] Code タブ内のセッションを開くとアプリがクラッシュ（v1.260618.0）— SwiftUI のメインスレッドスタックオーバーフロー](https://github.com/anthropics/claude-code/issues/70144)** — iPadOS 向け Claude Code アプリで Code タブ内のセッションを開こうとするたびにアプリがクラッシュしていた問題（修正済み、2026-10-06 にコメント追加）。SwiftUI のビュー階層が深くなる状況でメインスレッドのスタックオーバーフローが発生していた。iPad を iOS 開発のセカンドスクリーンとして使いながら Claude Code も同一端末で動かすスタイルの開発者に影響していた。ラベル: `bug`, `platform:ios`, `regression`。

## 📝 日本語コミュニティ

- 該当なし（2026-10-08 時点で Zenn / Qiita に iOS 文脈の新規記事は確認できず）

## 🌐 海外コミュニティ / Tips

- **[10 Tips to Maximize Claude Code for iOS Development（Medium: codetodeploy）](https://medium.com/codetodeploy/10-tips-to-maximize-claude-code-for-ios-development-29027edc51a7)** — iOS 開発で Claude Code を最大限に活用するための 10 のヒントをまとめた実践記事。「Claude Code は Swift や SwiftUI の深い理解の代替にはならない」という前提で、リクエストのスコープを 1 スクリーン・1 フレームワークに絞る方法、段階的なビルドサイクルを維持するコツ、`CLAUDE.md` へのアーキテクチャルールの記述方法など、実務投入した開発者の視点からまとめられている。

- **[Simon Willison の Swift プロジェクトメモ（simonwillison.net）](https://simonwillison.net/b/8821)** — 著名な開発者 Simon Willison が Claude Code を使って Swift アプリを開発した際の観察メモ。**SwiftUI は得意**（生成精度高い）だが **Swift Concurrency（async/await・actor）は混乱しやすい**と報告。コンパイラが "unable to type-check this expression in reasonable time" を出した際、ビュー本体を小さな computed property に分割するよう Claude に誘導すると自己修正できたという具体的な回避策も記録されている。UI テストには Playwright 相当のツールがなくスクリーンショット確認が依然必要な点も言及。

## 💡 今日のおすすめ実践 Tip

**v2.1.293 の Haiku 5.5 デフォルト化を機に「ビルドログ解析エージェント」のコンテキスト上限を見直す**

v2.1.293 からデフォルトの Haiku モデルが Claude Haiku 5.5 (1M コンテキスト) に変わった。これは iOS プロジェクトの **CI ビルドログ解析**に特に有効で、大型モノレポのフルビルドログ（数万行に及ぶ場合がある）を切り捨てることなく 1 回のプロンプトで渡せるケースが大幅に増える。

```bash
# CI ログをまるごと Haiku エージェントに渡す例
cat build.log | claude -p "このビルドログを解析して、エラーと警告を日本語でリストアップして。
iOS の警告（deprecated API / Swift Concurrency / Signing など）を優先してまとめて。"
```

以前は長大なログを渡すと Haiku のコンテキスト上限に引っかかりトークンが切り捨てられることがあった。1M コンテキストになったことで、`xcodebuild -resultBundlePath` で出力した xcresult のテキストダンプをそのまま投入するパターンも現実的になる。コストを抑えながら解析精度を上げたい場合は、まず Haiku でトリアージし、深掘りが必要な箇所だけ Sonnet に渡すという 2 段階ワークフローが有効。
