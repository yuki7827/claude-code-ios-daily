---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-07-28"
date: 2026-07-28 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

該当なし（本日時点で v2.1.220 以降の公式リリースは確認されていない。直近リリースは v2.1.220（2026-07-25）。）

## 🛠 GitHub の動き

- [Issue #81520 — Desktop iOS シミュレーターパネルが macOS 27 beta で crash-loop（OPEN / 2026-07-27）](https://github.com/anthropics/claude-code/issues/81520) — Apple Silicon (M5) + macOS 27.0 beta + Xcode 27.0 Beta 4 の環境で、Claude Desktop の iOS シミュレーターパネルが「Claude Code iOS Simulator is restarting after a crash. Try again in 1s.」を無限ループする問題。原因は 2 層。**Bug 1**：Xcode 27 beta 4 が `SimulatorKit.framework` を `Contents/Developer/Library/PrivateFrameworks/` から `Contents/SharedFrameworks/` に移動したため、ヘルパー（`claude-ios-sim`）の FBControlCore が旧パスを参照してフレームワーク読み込みに失敗する（同様の問題は argent・sim-use など FBSimulatorControl ベースのツールでも報告済み）。シンボリックリンクを追加する回避策あり（詳細は本日の Tip 参照）。**Bug 2**：SimulatorKit の読み込みが成功しても BitmapStream スレッドで Metal/CoreImage 初期化中に SIGABRT が発生し続けるブロッカー。こちらはユーザー側での回避策なし。`build` / `launch` アクションは正常動作するが、ライブ映像ストリーミング（パネルへの映像表示）は動作しない状態が続く。

- [Issue #81634 — iOS / claude.ai の Remote Control セッションでモードチップが初回ターン前に「Manual」と表示される（OPEN / 2026-07-27）](https://github.com/anthropics/claude-code/issues/81634) — Remote Control で `auto` モードのセッションを起動した直後、iOS アプリの Code タブでモードチップが「Manual」と表示されるバグ。実際にはセッションは `auto` で動作しており（tool_use も承認プロンプトなしで実行される）、最初のターンを送ると「Auto」に自動修正される。初回ターン前だけが間違ったラベルを表示するため「Manual のまま？」という誤解を招くリスクがある。**回避策**：チップを手動タップして変更しても動作上は変わらない（既に Auto）。最初の一言を送れば正しく表示される。なお関連 Issue #81635 では `permissions.defaultMode` 設定キーが公式の設定リファレンス表から抜けており、該当フィールドが存在しないと誤認されやすい問題も同日に報告されている。

## 📝 日本語コミュニティ

- [Claude Code 標準 Skill を iOS アプリ開発でどう使うか：公式ドキュメントを根拠に整理する（Qiita / 4q_sano）](https://qiita.com/4q_sano/items/0ecf0fc7353d751f601c) — `/code-review`・`/simplify`・`/security-review` などの Claude Code 標準スキルを iOS アプリ開発の各フェーズ（設計・実装・レビュー・テスト）にどう対応づけるかを、公式ドキュメントを根拠に整理した記事。SwiftUI View の状態管理レビューや非同期処理の安全性確認に `/code-review` を活用する具体例が参考になる。スキルを「いつ・何のために呼ぶか」が整理されており、チームで Claude Code を標準化する際の手引きとして使える。

## 🌐 海外コミュニティ / Tips

- [Copilot Vision と Claude Code で iOS アプリを新規作成して比較（Zenn / yamazaking）](https://zenn.dev/yamazaking/articles/copilot-vision-claude-code-ios) — 同一仕様の TODO アプリを Copilot Vision と Claude Code でそれぞれ作成して比較した記事。Claude Code は UserDefaults を使った永続化・MVVM 構成（Models / ViewModels / Views に分割）を自動採用した一方、Copilot Vision はアプリ終了でリセットされる単一ファイル構成を生成。コストは Claude Code で約 $0.17（≒ 25 円）。著者は「Claude Code が圧倒的に上」と結論づけつつ、Copilot Vision は Xcode 直結の手軽さに強みがあると評価している。初期開発フェーズでのアーキテクチャ品質の差が如実に出た比較。

## 💡 今日のおすすめ実践 Tip

**「macOS 27 beta + Xcode 27 beta 4 で iOS シミュレーターパネルが起動しない場合の回避策（Issue #81520 Bug 1）」**

Xcode 27 beta 4 は `SimulatorKit.framework` の格納場所を変更したため、Claude Desktop の iOS シミュレーターパネルが Framework 読み込みに失敗してクラッシュループに入ります。以下のシンボリックリンクを追加することで Bug 1 を回避できます（Bug 2 の Metal クラッシュは別途修正待ち）。

```bash
# Xcode 27 beta 4 の旧 PrivateFrameworks パスにシンボリックリンクを追加
mkdir -p "/Applications/Xcode-27.0.0-Beta.4.app/Contents/Developer/Library/PrivateFrameworks"
ln -s ../../../SharedFrameworks/SimulatorKit.framework \
  "/Applications/Xcode-27.0.0-Beta.4.app/Contents/Developer/Library/PrivateFrameworks/SimulatorKit.framework"
```

コマンド実行後、Claude Desktop を再起動してシミュレーターパネルを開き直してください。

なお Bug 2（BitmapStream の SIGABRT）はこの回避策後も残るため、現時点では**ライブ映像ストリーミング（パネルへの画面表示）は動作しない**状態が続きます。macOS 27 beta でも `simulator/build` と `simulator/build-and-run` ツールはパネルなしで正常動作するため、ビルドとシミュレーター起動の自動化は引き続き XcodeBuildMCP 経由で代替できます。Xcode 27 正式版または Claude Desktop ヘルパーの対応アップデートまでの暫定回避策として活用してください。
