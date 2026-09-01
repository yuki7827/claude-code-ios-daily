---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-09-02"
date: 2026-09-02 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- **v2.1.252（2026-08-31）リリース**（[Claude Code 変更ログ](https://code.claude.com/docs/en/changelog)）— iOS 開発に直接影響するバグ修正 4 件。① **一部 Mac で Bash コマンドが "task output swap refused" で失敗する問題を修正** — xcodebuild や fastlane など長時間かかる Bash コマンドがスワップ競合で中断していたケースが解消; ② **`.claude/settings.local.json` が存在しないプロジェクトで "always allow" が保存されない問題を修正** — 新規 iOS プロジェクトで MCP ツール権限を設定する際に設定が消えていた問題; ③ **Remote Control セッションがツール完了後に数分間スタールする問題を修正** — iPhone からの Remote Control 経由で iOS ビルドを監視しているときの接続劣化時の停止を解消; ④ **大容量出力の背景タスク通知が API リクエストサイズ上限を超えてエラーになる問題を修正** — xcodebuild の大量ログを伴うビルドタスクの完了通知が失敗するケースが解消

- **v2.1.257（2026-09-01）リリース**（[Claude Code 変更ログ](https://code.claude.com/docs/en/changelog)）— iOS 開発観点での主要変更点: ① **Claude Fable 5.1（`claude-fable-5-1`）をデフォルト Fable モデルとして追加** — 1M コンテキスト、$10/$50 per MTok、キャッシュリード $0.25/MTok。大規模 Swift プロジェクトの全体把握や iOS プロジェクトのコンテキスト集約に適した 1M ウィンドウが活用可能; ② **「Containment Escape」ルールを自動モードに追加** — クラウドメタデータ認証情報の取得・クロステナント操作・エグレス回避が環境未承認の場合は自動承認されなくなった。CI 環境で `ANTHROPIC_API_KEY` が漏洩するリスクを自動ガードする設計; ③ **`CLAUDE_CODE_SUBAGENT_MODEL_FORCE` 環境変数を追加** — iOS ビルド解析サブエージェントなど複数サブエージェントを使うワークフローで全サブエージェントのモデルを一括上書き可能になった; ④ **起動後に `.claude/` フォルダへ追加された設定が反映されない問題を修正** — ホットリロードが効くようになった; ⑤ **サブエージェントがミッドストリームのレスポンス切断で停止せず自動継続するように修正** — Xcode ビルドログのストリーミング中に切断されても中断しない; ⑥ **ワークディレクトリ外の初回ファイル読み込み前に一回限りの権限確認プロンプトを追加** — iOS プロジェクトで Derived Data や SPM キャッシュ外へのアクセス時の誤操作を防止; ⑦ **`/doctor` がスタールした seatbelt マスクファイルに警告を表示** — macOS 27 beta で Issue #80177 系の seatbelt クラッシュが発生している環境での診断補助; ⑧ **VSCode 拡張: モデル切り替え・セッション管理 UI の改善**（入力フッターへのモデルピル表示追加・「Delete session」→「Archive session」化）

## 🛠 GitHub の動き

- [Issue #79991 — iOS Simulator パネルが Xcode 27 (Device Hub) で SimulatorKit.framework パス変更により起動不能になるバグが修正（CLOSED / 2026-07-22 起票）](https://github.com/anthropics/claude-code/issues/79991) — Xcode 27 が `Simulator.app` を廃止して新しい **Device Hub** に置き換えたことに伴い、`SimulatorKit.framework` のパスが `Contents/Developer/Library/PrivateFrameworks/` から `Contents/SharedFrameworks/` に移動した。Claude Code の `claude-ios-sim` ヘルパーが旧パスをハードコードしていたため、Xcode 27 環境で `attach` が `Error Domain=com.facebook.FBControlCore Code=0 … path '…/PrivateFrameworks/SimulatorKit.framework' does not exist` で失敗し、iOS Simulator パネル全機能が使用不能になっていた。v2.1.257 で修正済み（フレームワークパスを xcode-select の指す Xcode バージョンに基づいて動的解決するよう変更）。**暫定回避策（修正前）**: `xcode-select` を Xcode 26 系に向け直すとシミュレータ操作は復帰するが、Xcode 27 SDK 依存のビルドが失敗するトレードオフがあった。最新版へのアップデートで根本解消。

## 📝 日本語コミュニティ

- [SwiftUI × Claude Code で小規模事業者向け iOS アプリを MVP からApp Store 申請まで全自動で作った話（Qiita / sorabcjanne1）](https://qiita.com/sorabcjanne1/items/f0f6bc44c91d38d5e9c2) — SwiftUI・SwiftData・PDFKit を組み合わせた iOS アプリを Claude Code × XcodeBuildMCP で書き始め、コードレビュー・修正・ビルド・テスト・スクリーンショット生成・App Store Connect へのアップロードをほぼ自動化した実体験記事。人間がタッチしたのは初期設定とレビュー応答のみと報告されており、エンドツーエンドの自動化ラインとして参考になる。

- [【第 2 弾】VS Code の Claude Code で「iOS コードレビュー → 修正 → Xcode ビルド確認 → テスト項目作成」を自動化する（Qiita / 4q_sano）](https://qiita.com/4q_sano/items/effe51de5e9654777f1e) — 前回（第 1 弾）で構築した VS Code + Claude Code 環境をもとに、PR のコードレビューから修正・ビルド検証・テスト仕様書生成までを一気通貫で回すワークフローを解説した記事。XcodeBuildMCP の `buildAndTest` と Claude Code の `/review` を組み合わせた自動化パイプラインが詳しく紹介されている。

## 🌐 海外コミュニティ / Tips

- [The NEW Way to Build Mobile Apps with Claude Code & Fable 5 (iOS Simulator)（YouTube）](https://www.youtube.com/watch?v=UZYW8BnclvI) — Fable 5（`claude-fable-5-1`）を Claude Code に接続し、iOS Simulator の MCP ツールと組み合わせてモバイルアプリ UI を反復開発する動画チュートリアル。1M コンテキストを活かして SwiftUI ファイル全体を一括読み込み、エラー解析→修正→シミュレータでの確認までをフルエージェントで実演している。

- [Making Claude Code Work for iOS Development（Tate Jennings ブログ）](https://www.tatejennings.com/blog/making-claude-code-work-for-ios-development) — Claude Code を iOS 開発で実用的に運用するための実践的なノウハウをまとめた記事。CLAUDE.md への Xcode プロジェクト構造の明記・SwiftUI ファイルのコンテキスト管理・XcodeBuildMCP との組み合わせ方が具体的な設定例とともに紹介されており、「なぜ Claude Code はデフォルト設定のままだと iOS 開発で嚙み合わないのか」という視点が特に参考になる。

## 💡 今日のおすすめ実践 Tip

**「`CLAUDE_CODE_SUBAGENT_MODEL_FORCE` で iOS ビルド解析サブエージェントを Fable 5.1 に固定する」**

v2.1.257 で追加された `CLAUDE_CODE_SUBAGENT_MODEL_FORCE` 環境変数を使うと、サブエージェントのモデルをメインセッションと切り離して指定できます。1M コンテキストの Fable 5.1（`claude-fable-5-1`）を大規模ログ解析に、コスト効率の良いモデルを軽量タスクに、という使い分けが `.env` 1 行で実現します。

**iOS CI での活用例（GitHub Actions）**

```yaml
# .github/workflows/claude-ios-review.yml
env:
  # サブエージェント（ビルドログ解析・テスト結果要約など大容量入力タスク）は
  # 1M コンテキストの Fable 5.1 で処理する
  CLAUDE_CODE_SUBAGENT_MODEL_FORCE: claude-fable-5-1

jobs:
  review:
    runs-on: macos-15
    steps:
      - uses: actions/checkout@v4
      - name: Run Claude Code review with XcodeBuildMCP
        run: |
          claude --model claude-sonnet-5 \
            "XcodeBuildMCP で full test suite を回してエラーを分析し、修正 PR を作ってください"
```

**ポイント**

- メインモデル（`--model`）を `claude-sonnet-5` などコストを抑えたモデルにしつつ、ビルドログや大量 Swift ファイルを読み込むサブエージェントは自動的に Fable 5.1 に昇格されます。
- `CLAUDE_CODE_SUBAGENT_MODEL_FORCE` は環境変数なので、プロジェクト単位で `.env` または CI の Environment Secrets として管理でき、CLAUDE.md に書かず済みます（Public リポのセキュリティ規約との相性も良い）。
- 1M コンテキストのおかげで、ビルドログ + 全 Swift ファイル + テスト結果を一括ロードして「このエラーの根本原因はどこか」を一気に回答できるシナリオが現実的になりました。
