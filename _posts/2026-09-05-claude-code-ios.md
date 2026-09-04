---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-09-05"
date: 2026-09-05 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- **v2.1.261（2026-09-04）リリース**（[Claude Code 変更ログ](https://code.claude.com/docs/en/changelog)）— iOS 開発者に直結する変更が複数。① **`bashOutputMaxChars` / `taskOutputMaxChars` 設定を追加（最大 128K 文字）** — これまでデフォルト上限でスクロールアウトしていた xcodebuild の長いビルドログやテスト結果がまるごと Claude に届くようになる（`settings.json` の `bashOutputMaxChars` を `131072` に設定するだけ）。② **`/skill-doctor` スラッシュコマンドを追加** — ロード済みスキルのうち実際には呼び出されていないものとそのコンテキストコストを一覧表示し、必要なスキルのみ保持してプロンプトのトークンを節約できる。iOS 開発で追加した `/run`・`/code-review` 等のスキルが本当に機能しているか確認するのに便利。③ **`--append-subagent-system-prompt-file` フラグを追加** — 大きなサブエージェントシステムプロンプトをファイルから注入できる。④ **Remote Control の安定性改善** — セッション同期の複数バグ修正（iPhone Remote Control でビルドジョブを管理しているときに特に恩恵）。

- **v2.1.260（2026-09-03）リリース**（[Claude Code 変更ログ](https://code.claude.com/docs/en/changelog)）— ① **`/diff` パネルを追加** — フルスクリーンモードで会話の横に未コミット変更を常時表示できるようになる（`/diff` でトグル）。Claude が Swift ファイルを編集するたびに何が変わったか即確認できる。② **`/cost` コマンドにプロンプトキャッシュミス診断を追加** — キャッシュが効いていない原因を把握でき、CLAUDE.md や繰り返しプロンプトのキャッシュ最適化に役立つ。③ **バグ修正: サブエージェントモデル 404 エラー発生時にセッションのフォールバックモデルへ自動切り替え** — iOS CI でモデル指定のサブエージェントが 404 になって止まるケースが解消。

## 🛠 GitHub の動き

- [Issue #91870 — Function Hooks: TypeScript ミドルウェアでプラグインを 10x 強力にする提案（anthropics/claude-code / OPEN 2026-09-03）](https://github.com/anthropics/claude-code/issues/91870) — Claude Code プラグインを Express/Koa のような TypeScript ミドルウェアで拡張する "Function Hooks" の RFC。`$` オブジェクト経由でファイル読み書き・ネットワーク・UI 描画などの副作用を明示化・監査可能にし、管理者がプラグインの権限を細粒度に制御できる設計。まだ未リリースでフィードバックを募集中。iOS 開発での応用例として「ツール呼び出しを横断してキーチェーン情報やプロビジョニングプロファイルの秘密をモデル入力からマスキングする」「ビルド前に必ず `xcodebuildmcp setup` の完了を強制するバリデーションレイヤー」などが考えられる。Anthropic の [@ClaudeDevs](https://x.com/ClaudeDevs/status/2095572891941351550) が X でデモ動画を公開しており、注目度が高い。

- [Issue #80177 — [Desktop] iOS Simulator パネルが macOS 27.0 beta で Metal/CoreImage クラッシュにより "Attach a simulator" から進まない（anthropics/claude-code / OPEN）](https://github.com/anthropics/claude-code/issues/80177) — Claude Code Desktop の iOS Simulator パネルで使われる `claude-ios-sim` ヘルパーが macOS 27 beta 環境の Metal シェーダーキャッシュ配置変更に追随できていない問題。macOS 27 beta に移行済みの開発者は引き続き影響を受ける可能性がある。暫定回避策は XcodeBuildMCP の `launch_sim` / `get_sim_screenshot` ツールを CLI から直接使うか、macOS 26.x 安定版で作業を継続すること。

## 📝 日本語コミュニティ

- [iOSアプリ開発未経験者が Claude Code を使って1週間でリリースしたお話（Qiita / chatrate）](https://qiita.com/chatrate/items/bd2703d68a2bb8fffe08) — Swift の知識がほぼない状態から Claude Code だけを使い、7日でアプリを App Store に提出した記録。「Claude Code に何をどう伝えるかが開発速度を左右する」という結論で、UI 修正や機能追加のたびに CLAUDE.md を更新し続ける運用が実務感覚で紹介されている。XcodeBuildMCP との組み合わせはなく Claude Code 単体での Xcode プロジェクト管理が主軸。

- 該当なし（9月5日時点で新規の Zenn 記事は確認できず）

## 🌐 海外コミュニティ / Tips

- [Anthropic Wants Claude Code to Run TypeScript Middleware Like Express（AlphaSignal）](https://alphasignal.ai/news/anthropic-wants-claude-code-to-run-typescript-middleware-like-express) — Issue #91870 の Function Hooks 提案を英語圏に向けて解説した記事。「Express/Koa と同様の `next()` 継続モデルでフックを連鎖できる」「`$` 経由でのみ副作用を発生させることで管理者がプラグインの権限を剥奪できる」という設計の要点が整理されており、iOS チームで独自プラグインの導入を検討する際の判断材料になる。

- 該当なし（dev.to 等の海外記事は9月5日時点で iOS 文脈の新規記事を確認できず）

## 💡 今日のおすすめ実践 Tip

**「`bashOutputMaxChars` を設定して xcodebuild のフルログを Claude に渡す」**

v2.1.261 で追加された `bashOutputMaxChars` / `taskOutputMaxChars` は、iOS 開発で特に効果が大きい設定です。xcodebuild のビルドエラーやテスト失敗メッセージは長く、デフォルト上限では末尾が切れてしまい、Claude がエラーの根本原因を特定できないことがありました。

**`.claude/settings.json` に追記するだけで有効化**

```json
{
  "bashOutputMaxChars": 131072,
  "taskOutputMaxChars": 131072
}
```

これで最大 128K 文字（約 128KB）のコマンド出力を Claude がそのまま受け取れます。

**効果が出やすいシーン**

```
# ❌ これまで（デフォルト上限で末尾が切れていた例）
xcodebuild test -scheme MyApp -destination 'platform=iOS Simulator,...'
→ Claude: "出力が途中で切れているため、失敗の詳細が確認できません"

# ✅ bashOutputMaxChars: 131072 設定後
xcodebuild test -scheme MyApp -destination 'platform=iOS Simulator,...'
→ Claude: "テスト失敗は UserRepositoryTests.testFetchUser の L.42 で
   'Expected 3 items but got 2' — モックの設定が不足しています。
   修正案は..."
```

**XcodeBuildMCP との使い分け**

XcodeBuildMCP を使う場合は MCP プロトコル経由でビルド結果が届くため、この設定は Bash ツールで直接 `xcodebuild` を呼ぶシナリオに効きます。XcodeBuildMCP の `taskOutputMaxChars` は MCP ツール応答が `taskOutput` として渡る場合に適用されるため、両方設定しておくと安全です。
