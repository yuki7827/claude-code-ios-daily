---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-08-26"
date: 2026-08-26 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- **v2.1.243（2026-08-25）リリース**（[Claude Code 変更ログ](https://code.claude.com/docs/en/changelog)）— iOS 開発観点での主な変更点: ① **`modelPicker` 設定追加** — `/model` ピッカーに表示するモデルをラベル付き・順序付きでカスタマイズできるようになった。Vertex/Bedrock ID も指定可能で、「レビュー用 Opus・実装用 Sonnet・スキャン用 Haiku」のように用途別にモデルを整理して素早く切り替えるのに有用。エンタープライズ iOS チームが managed setting として全員に配布することもできる; ② **`promptCacheTtl` / `subagentPromptCacheTtl` 設定追加** — API キーまたはクラウドプロバイダー（Bedrock/Vertex）利用ユーザー向けに、メインセッションのプロンプトキャッシュ TTL（デフォルト 1 時間）とサブエージェントのキャッシュ TTL（デフォルト 5 分）を個別に制御できるようになった。Xcode ビルドログや XCTest 結果など大量のコンテキストを積む iOS セッションでキャッシュ効率を調整できる; ③ **`modelPricing` managed 設定追加** — 組織で契約した per-model 料金をハードコードすることで、`/usage` のコスト表示が実際の請求と一致するようになった。iOS チームのコスト可視化に有用; ④ **`/login` にコンソール経由の keyless sign-in を追加** — Anthropic Console でセッションを認証する方法が増えた; ⑤ **`/usage` に Loops ブレークダウンを追加** — ループ別の実行回数・合計トークン・1 回あたりのトークン数・最終実行時刻を確認できるようになった。定期実行の iOS キャッチアップや CI ループのコスト分析に役立つ; ⑥ **`/tasks` にモデル・effort レベルを表示** — 実行中・完了済みサブエージェントが何のモデルで何の effort で動いているか確認できるように; ⑦ **バグ修正**: MCP server reconnection・auto mode availability・`/resume` pagination・ターミナル/UI の各種修正

- **v2.1.245（2026-08-25）リリース**（[Claude Code 変更ログ](https://code.claude.com/docs/en/changelog)）— glibc 2.44 搭載 Linux ディストリビューション（Arch Linux・CachyOS・Fedora Rawhide）での起動クラッシュを修正。Linux ランナー上で Claude Code を使う iOS CI ワークフローに影響していた可能性がある。

## 🛠 GitHub の動き

- [Issue #80177 — iOS Simulator パネルが macOS 27.0 beta で Metal/CoreImage の SIGABRT によりクラッシュループ（OPEN / 2026-07-22 起票・2026-08-25 更新）](https://github.com/anthropics/claude-code/issues/80177) — 2026-08-24 でカバーした Issue #88504（seatbelt プロファイルの Metal キャッシュディレクトリ write 拒否問題）とは異なる macOS 27.0 beta 固有のバグ。こちらは `claude-ios-sim` ヘルパーが CoreImage の `CIContext` を初期化する際に Metal が `_MTLBinaryArchive` に nil オブジェクトを挿入しようとして SIGABRT（クラッシュループ）が発生する問題。パネルは「Attach a simulator」の状態で固まり続ける。Metal シェーダーキャッシュの削除・iOS ランタイム変更・プロセス再起動では解消しない。**現時点で有効な回避策なし**（稀に多数回再起動後にパネルが立ち上がる事例もあるが再現性はない）。Xcode 26.6 + macOS 27.0 beta (26A5388g, Apple Silicon) の組み合わせで発生が確認されている。

- **iOS 関連 issues が 2026-08-25 に複数クローズ** — v2.1.243 のバグ修正と対応して以下が一括クローズ: [#70338（iOS アプリで大きなセッションを開くと Code タブがクラッシュ）](https://github.com/anthropics/claude-code/issues/70338)・[#70164（iOS アプリで「New Code Session」タップ直後にクラッシュ ― 6/22 更新で発生した退行バグ）](https://github.com/anthropics/claude-code/issues/70164)・[#77387（Remote Control: Mac で作ったセッションを iOS で再開しようとすると "active worker" 競合 + close code 4090 で接続失敗）](https://github.com/anthropics/claude-code/issues/77387)。Remote Control と iOS アプリの信頼性改善が v2.1.243 に含まれていることが伺える。

## 📝 日本語コミュニティ

- [SwiftUI × Claude Codeで小規模事業者向けiOSアプリをMVPからApp Store申請まで全自動で作った話（Qiita / sorabcjanne1）](https://qiita.com/sorabcjanne1/items/f0f6bc44c91d38d5e9c2) — SwiftUI・SwiftData などを使って MVP 設計から App Store 申請まで Claude Code だけで完結させた実践事例。実際の申請フローに沿った手順を詳解しており、ゼロからプロダクトを作り上げたい開発者・スモールチームの参考になる内容。

- [【第2弾】VS CodeのClaude Codeで「iOSコードレビュー → 修正 → Xcodeビルド確認 → テスト項目作成」を自動化する（Qiita / 4q_sano）](https://qiita.com/4q_sano/items/effe51de5e9654777f1e) — VS Code 拡張版 Claude Code を使った iOS コードレビュー〜ビルド〜テスト項目自動生成のシリーズ第2弾。前回記事から更にワークフローを深掘りした実践的な続編。

## 🌐 海外コミュニティ / Tips

- [Xcode 26.3 + Claude Agent — Model Swapping, MCP, Skills, and Adaptive Configuration（fatbobman.com）](https://fatbobman.com/en/posts/xcode-263-claude/) — Swift/SwiftUI 開発者として知られる Fatbobman による Xcode 26.3 + Claude Agent SDK 解説記事。モデル切り替えの実用テクニック・MCP の設定方法・スキルと適応設定（Adaptive Configuration）を実務の観点から整理した内容。英語・日本語両対応で発信されているため、ソース原文の確認にも適している。

- [claude-code-ios-dev-guide（keskinonur / GitHub、829 stars）](https://github.com/keskinonur/claude-code-ios-dev-guide) — Swift/SwiftUI iOS 開発向けに Claude Code CLI を最適化するための包括的なガイドリポジトリ。PRD 駆動ワークフロー・Ultrathink（拡張思考）・Plan Mode・XcodeBuildMCP 統合・スラッシュコマンド・フックシステム・サンドボックス設定まで、実運用に必要な設定をまとめて解説している。CLAUDE.md テンプレートの質が高く、iOS プロジェクトの初期設定の参考として有用。

## 💡 今日のおすすめ実践 Tip

**「v2.1.243 の `modelPicker` + `promptCacheTtl` で iOS 開発セッションを用途別に最適化する」**

v2.1.243 で追加された 2 つの設定を組み合わせると、Xcode ビルドログや XCTest 結果など大きなコンテキストを扱う iOS 開発セッションのコストとレスポンス速度を細かくコントロールできます。

**`modelPicker` — 用途別モデルをラベル付きで整理する**

`.claude/settings.json` に以下を追加すると `/model` で表示されるモデルを絞り込み、素早く切り替えられます:

```json
{
  "modelPicker": [
    { "label": "Opus — アーキテクチャ設計 / コードレビュー", "model": "claude-opus-5" },
    { "label": "Sonnet — 実装 / リファクタリング", "model": "claude-sonnet-5" },
    { "label": "Haiku — ログスキャン / 軽量タスク", "model": "claude-haiku-4-5-20251001" }
  ]
}
```

iOS 開発でよくある用途ごとにモデルをラベル付けしておくことで、「今は実装フェーズだから Sonnet」「ビルドログのエラーだけ洗い出すから Haiku」と素早く切り替えられます。

**`promptCacheTtl` / `subagentPromptCacheTtl` — ビルドログが溜まるセッションのキャッシュを調整する**

API キーまたは Bedrock/Vertex 経由の利用環境では以下で TTL を調整できます:

```json
{
  "promptCacheTtl": 3600,
  "subagentPromptCacheTtl": 300
}
```

`promptCacheTtl: 3600`（1 時間）はデフォルト値ですが、明示することで意図を明確にし、将来のデフォルト変更の影響を受けないようにできます。`subagentPromptCacheTtl: 300`（5 分）はサブエージェントがすぐに別タスクへ切り替わるケースに合わせた短い TTL です。

Xcode のビルドログを毎回コンテキストに積む CI ワークフローでは、**ビルドログをコンテキストの最初に置く**ことでプロンプトキャッシュのヒット率が上がりやすくなります（ログが変わらない限り同じキャッシュキーになるため）。

**iOS CI での活用例（GitHub Actions）**

```yaml
env:
  ANTHROPIC_DEFAULT_MODEL: claude-sonnet-5  # v2.1.236 で追加された環境変数
  # promptCacheTtl は .claude/settings.json で管理
steps:
  - name: Claude Code — ビルドエラー解析
    run: |
      claude --print "以下のビルドログのエラーを分類して修正案を提示してください:
      $(cat build.log)"
```

CI ランナーを Linux で運用している場合は v2.1.245 の glibc 2.44 クラッシュ修正が適用された最新版への更新（`claude update`）も忘れずに確認してください。
