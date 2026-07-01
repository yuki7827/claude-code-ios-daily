---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-07-02"
date: 2026-07-02 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Claude Code v2.1.197 — Claude Sonnet 5 がデフォルトモデルに（2026-06-30）](https://www.anthropic.com/news/claude-sonnet-5) — ネイティブ **1M トークンコンテキストウィンドウ**（ベータフラグ不要）を備えた Sonnet 5 が Claude Code のデフォルトになった。5 万行クラスの SwiftUI プロジェクトでも、全ソース＋ビルドログ＋テスト結果をまとめて投入して作業指示が可能になる。プロモーション価格 $2/$10/MTok（2026-08-31 まで）。SWE-bench Verified 92.4%（Opus 4.6 比 +12pt）とコーディング性能も大幅向上。なお、Sonnet 5 は新トークナイザーを採用しており、同じ入力が従来比 1.0〜1.35 倍のトークン数になる場合があるため `/usage` でコスト監視を推奨。

- [Claude Code v2.1.196（2026-06-29）— org デフォルトモデル設定・Stream watchdog・/code-review 効率化](https://code.claude.com/docs/en/changelog) — iOS 開発チームに影響する 5 つの変更。①**org デフォルトモデル**：管理者が org コンソールから Claude Code のデフォルトモデルを一括設定可能（`/model` に「Org default」表示）— iOS CI 全体で同じモデルを強制できる。②**Stream watchdog がデフォルト有効化**：5 分間無応答のセッションを自動中断・再試行（`CLAUDE_ENABLE_STREAM_WATCHDOG=0` で無効化）— 長時間 `xcodebuild` と並走する際の stale 検知が不要になる。③**`/code-review` 効率化**：5 つのファインダーを統合し ~25% トークン削減 — iOS PR のコストが下がる。④**ファイル添付クリック対応**：Cmd/Ctrl-click で Finder/Explorer に表示。⑤**セキュリティ修正**：コミット済み `.claude/settings.json` からの MCP サーバー自動承認を廃止、信頼されていないワークスペースでは `⏸ Pending approval` 表示になる。

## 🛠 GitHub の動き

- [getsentry/XcodeBuildMCP — キーボード制御ツールとテスト別タイミング出力を追加](https://github.com/getsentry/XcodeBuildMCP) — 最近の更新で **`toggle_software_keyboard`・`toggle_connect_hardware_keyboard`** が追加され、Claude が iOS シミュレータのソフトウェアキーボードの表示切替とハードウェアキーボードの接続切断をプログラムから制御できるようになった。また **テスト別タイミング出力**（`XCODEBUILDMCP_SHOW_TEST_TIMING=1`）が追加され、遅いテストを Claude が自律的に特定して改善提案できる構成が整った。さらに **プラットフォーム別セットアップウィザード**（iOS/macOS/tvOS/watchOS/visionOS）が加わり、初回設定で対象プラットフォームを選ぶと最適なワークフロー設定が自動生成される。

- [kylehughes/apple-platform-build-tools-claude-code-plugin](https://github.com/kylehughes/apple-platform-build-tools-claude-code-plugin) — iOS ビルド・テスト・アーカイブを Claude Code プラグインとして統合する MIT ライセンスのプラグイン（57 stars）。**Agent Skill**（xcrun/xcodebuild/swift build/simctl/devicectl の包括的リファレンス、コード署名・プロファイル・配布ワークフローも収録）と **Subagent**（プロジェクト構造自動検出・スキーム特定・シミュレータ管理・ビルド実行・デバッグシンボル抽出・パフォーマンスプロファイリング）の 2 コンポーネント構成。iOS/iPadOS/macOS/tvOS/watchOS/visionOS 全 Apple プラットフォーム対応。

## 📝 日本語コミュニティ

- 本日時点で新規確認できた iOS × Claude Code の日本語記事なし。Sonnet 5 リリースを受けた日本語解説記事は Zenn・Qiita に複数公開されているが、iOS 固有の実践報告はまだ出揃っていない段階。

## 🌐 海外コミュニティ / Tips

- [Claude Code 2.1.196: Org Default Models, Stream Watchdog, and a Tighter Remote Control Boundary（TheRouter.ai）](https://therouter.ai/news/claude-code-2196-org-default-models-stream-watchdog-routing/) — v2.1.196 の全変更点を解説した英語記事。Stream watchdog の内部動作（5分無応答 → 自動中断・再試行）と org デフォルトモデルの設定手順を詳述。iOS CI での活用観点からも参考になる。

- [XcodeBuildMCP Complete Guide 2026（mcp.directory）](https://mcp.directory/blog/xcodebuildmcp-complete-guide-2026) — Sentry 傘下になった XcodeBuildMCP の 82 ツールを体系的に解説した英語ガイド。Apple 純正 Xcode MCP との役割分担（ビルド・テスト・シミュレータ操作は XcodeBuildMCP、SwiftUI プレビュー・REPL・ドキュメント検索は純正 MCP）を整理しており、組み合わせ構成の設計に役立つ。

## 💡 今日のおすすめ実践 Tip

**「Sonnet 5 の 1M コンテキストで iOS 大規模プロジェクトをゼロショット把握してリファクタリングする」**

v2.1.197 から Claude Code のデフォルトになった Sonnet 5 の 1M トークンコンテキストは、中規模 iOS プロジェクト全体をまるごと渡してから作業指示するワークフローを現実的にします。

**実用例：TCA → Swift 6.2 strict concurrency 移行**

```bash
# プロジェクト全ソースをまとめて投入（例: 約 3 万行 → 約 400k トークン）
find . -name '*.swift' -not -path './.build/*' | sort | \
  xargs awk 'FNR==1{print "// --- " FILENAME " ---"} {print}' | \
  claude -p "
上記の Swift ファイル全体を読み込んだうえで、
TCA の Store・Reducer を Swift 6.2 の strict concurrency に段階移行してください。

手順：
1. @MainActor が必要な型を特定して宣言を追加
2. Sendable 準拠が必要な値型を洗い出して修正
3. async/await に置き換えるべき非同期処理を修正
4. XcodeBuildMCP でビルドが通るまで自律的に修正を続ける
5. 変更ファイル一覧を最後に出力

注意：実装ファイルのみ変更し、テストファイルは手を付けないこと。"
```

**コスト管理のポイント**

Sonnet 5 は新トークナイザーにより同じ入力が従来比最大 1.35 倍のトークンになる場合があります。大量のソースを流す前に `/usage` でセッション消費量を確認し、必要に応じて `/cd` でディレクトリを絞るかファイルをフィルタリングして投入量を調整してください。

**Stream watchdog との組み合わせ**

v2.1.196 でデフォルト有効化された Stream watchdog が、長時間のリファクタリング中に `xcodebuild` の応答が途絶えた場合でも自動的にセッションを回復してくれるため、数十ファイルにまたがる移行作業を人間が見張らなくても進め続けられます。
