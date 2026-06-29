---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-06-30"
date: 2026-06-30 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- 本日時点の最新リリースは [v2.1.195（2026-06-26）](https://github.com/anthropics/claude-code/releases/tag/v2.1.195) のまま変更なし。直近の変更点は [06-28 の記事](https://yuki7827.github.io/claude-code-ios-daily/) を参照。

## 🛠 GitHub の動き

- [iOS Claude Code Setup Workflow（gist / joelklabo）](https://gist.github.com/joelklabo/36cbd765b4a3a47c7a03cb2685de1162) — 前日紹介の「iOS Project Claude Code Setup Prompt」gist の作者 joelklabo による実装ワークフロー Gist。`.claude/` 配下にカスタムコマンド（`/build-device`・`/test-single`・`/swift6-check`）・フック・コンテキスト検出スクリプトを配置し、XcodeBuildMCP と連携させる具体的なディレクトリ構成を公開。「コマンドは成果物である。Claude がドライバーなら Makefile に埋めるな。エージェントが最初に見る場所に置け」という設計哲学が一貫しており、前回の設定プロンプト gist と組み合わせることで、iOS プロジェクトへの Claude Code 導入をゼロから 2 ファイルで完結させられる。

## 📝 日本語コミュニティ

- [Claude Code 6月新機能 — 5段階エージェントと/cdコマンドで自律開発を実現する（Qiita / kai_kou）](https://qiita.com/kai_kou/items/81cee59a85d82535e986) — v2.1.172（2026-06-10）で実装されたサブエージェントの最大 **5 階層ネスト**と、v2.1.169 で追加された **`/cd` コマンド**を軸に 6 月のアップデートをまとめた記事。iOS 開発への応用として「1 階層目：コーディネーター → 2 階層目：SwiftUI 実装 / ビジネスロジック / テスト → 3 階層目：XcodeBuildMCP ビルド確認 / コードレビュー」といった階層分割が自然に成立する。`/cd` は monorepo 構成（`iOS/`・`Backend/`・`Shared/`）をまたぐ作業でコンテキストをリセットせず作業ディレクトリを切り替えられる点が特に有用。レートリミット時のフォールバックモデル自動切替も同月に追加された。

- [Claude Codeのネスト型サブエージェント入門 — 最大5階層の設計とトークン設計の勘所（Qiita / kai_kou）](https://qiita.com/kai_kou/items/618da2497af1c1bf0f91) — 上記の続編として 5 階層ネストサブエージェントの設計手法とトークン消費の最適化を解説した専論。深いネストはコンテキスト汚染のリスクがあるため、各層でコンテキストを絞って渡す設計パターンを紹介。iOS ビルドログ（xcodebuild の大量出力）を上位エージェントに流さず下位エージェント内で処理させる構成が典型例として挙げられており、コンテキスト節約と自律ビルドループの両立を学べる。

- [Claude Codeで2日・10時間のiOSアプリ開発 — うまくいったこと・限界・コツを全部書く（Zenn / kenty_vibe）](https://zenn.dev/kenty_vibe/articles/claude-code-2days-app) — 週末 2 日間・実作業 10 時間で iOS アプリを Claude Code で開発した実録。うまくいったこと・詰まった箇所・実践上のコツを全公開しており、短時間 PoC でどこまで委任できるか・どこで人手が必要かのリアルな感触をつかめる記事。

## 🌐 海外コミュニティ / Tips

- [Claude Code for iOS: Shipping a Real App in 18 Days（Nodes and Edges / theyawns.com）](https://theyawns.com/2026/06/24/claude-code-for-ios-shipping-a-real-app-in-18-days/) — 野球スコアリングアプリ「BaseballScorer」を初回コミット（2026-03-28）から App Store リリース（4/15）まで **18 日間**で完成させた開発記。SwiftUI + SwiftData 構成で、1.0 → 1.3 まで実ユーザーフィードバックを受けながら安定したリリースサイクルを維持。著者は「Claude が作った」でも「オートコンプリートを使っただけ」でもなく、**ビジョンと判断力は人間、退屈な実装と繰り返し作業は Claude** という明確な分業の結果だと総括している。個人開発者が Claude Code を使って市場に出る際の現実的なペース感・品質感を把握するのに有用な一次資料。

## 💡 今日のおすすめ実践 Tip

**「5 階層ネストサブエージェントで iOS ビルド → テスト → コードレビューを自律ループ化する」**

v2.1.172 で解禁されたサブエージェントの 5 階層ネストを使うと、iOS 開発のビルドサイクルを階層的に自律化できます。

```
# Claude Code への指示例（/effort high で実行）

「以下の 3 層サブエージェント構成で、
 MyApp-iOS の PR #42 をビルド・テスト・レビューして：

  1. ビルドエージェント
     → XcodeBuildMCP で Debug ビルドを実行
     → ビルドエラーがあれば自律修正して再ビルド（最大 3 回）

  2. テストエージェント（ビルド成功後に起動）
     → XcodeBuildMCP で swift test を実行
     → 失敗テストを特定して修正候補を提示

  3. レビューエージェント（テスト通過後に起動）
     → /code-review コマンドで差分をレビュー
     → SwiftUI のアクセシビリティ・iOS HIG 準拠をチェック

  最終結果を 1 つのサマリーで返して。
  xcodebuild の詳細ログは上位エージェントに流さないこと。」
```

**ポイント：ビルドログをエージェント内に閉じ込める**

`xcodebuild` の出力は膨大でコンテキストを汚染しやすいため、1 のビルドエージェントがログを自分で処理し「成功 / エラー行のみ」を上位に返す設計が重要です。`XCODEBUILDMCP_HEADLESS_LAUNCH` を設定してバックグラウンドで起動させると、Mac の画面を奪われずに済みます。

**`/cd` で monorepo も跨げる**

```
# iOS ディレクトリで Swift 実装 → バックエンドに移動してAPI確認
/cd iOS/
（SwiftUIコードを修正）
/cd Backend/
（API エンドポイントの仕様を確認）
/cd iOS/
（呼び出し側を修正）
```

セッションを切らずにディレクトリを往復できるので、API 変更を受けて iOS 側も修正するフローでコンテキストが保持されます。
