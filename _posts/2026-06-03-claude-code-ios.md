---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-06-03"
date: 2026-06-03 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Claude Code v2.1.160（2026-06-02）](https://code.claude.com/docs/en/changelog) — Dynamic Workflows の起動キーワードが `workflow` から **`ultracode`** に改名（`/effort ultracode` でセッション全体に適用可）。`acceptEdits` モードがビルドツール設定ファイル（`.npmrc`・`.yarnrc*`・`.bazelrc`・`.devcontainer/` 等）書き込み前に確認プロンプトを追加し、意図しないビルド環境の変更を防止。シングルファイルへの `grep`/`egrep`/`fgrep` 実行後は別途 `Read` なしで `Edit` が可能になり、検索→即編集が 1 ステップ短縮。CJK IME 入力位置のズレも修正。

## 🛠 GitHub の動き

- [getsentry/XcodeBuildMCP v2.6.0（2026-06-01）](https://github.com/getsentry/XcodeBuildMCP/releases) — UI オートメーションが大幅強化。**`wait_for_ui`**（述語が満たされるまでポーリング）・**`batch`**（複数アクションを 1 呼び出しにまとめる）・**`drag`**（ドラッグジェスチャー）・**`snapshot_ui`**（安定した要素参照＋画面ハッシュを返す）の 4 ツールを追加。アクション後に「再利用可能な UI コンテキスト」を返すことで、テストシナリオでウォールクロック時間 **約 70% 削減**・トークン使用量 **約 68% 削減**・ツール呼び出し **約 76% 削減** を実現。フォーカス不要の自動実行向け `XCODEBUILDMCP_HEADLESS_LAUNCH` モードも追加。

- [getsentry/XcodeBuildMCP v2.6.1/v2.6.2（2026-06-02）](https://github.com/getsentry/XcodeBuildMCP/releases) — v2.6.1: エージェントが `UITabBar` の項目を切り替えられるよう修正（#439・#441）。v2.6.2: `xcodebuildmcp upgrade` がパッケージマネージャーのキャッシュではなく GitHub 最新リリースを参照するよう修正。

- [twostraws/SwiftUI-Agent-Skill（v1.1.0）](https://github.com/twostraws/swiftui-agent-skill) — *Hacking with Swift* の Paul Hudson が作成した SwiftUI 特化スキル。AI が繰り返す典型ミス（VoiceOver ボタンの非可視化・deprecated API の誤用・パフォーマンス劣化パターン）を直接ターゲットにしたルールを収録。Claude Code・Codex・Gemini・Cursor いずれにも対応。

## 📝 日本語コミュニティ

該当なし — 本日時点で新規の iOS × Claude Code 日本語記事は確認できず。

## 🌐 海外コミュニティ / Tips

- [WWDC 2026（6/8-12）](https://developer.apple.com/wwdc26/) — 来週月曜（日本時間 6/9 午前 2 時）に Keynote 開幕。iOS 27 / macOS 27 Developer Beta が即日公開予定。AI 強化版 Siri・新 SwiftUI API・Apple Intelligence 拡充が期待される。Claude Code ＋ XcodeBuildMCP で Beta テストを素早く回せる環境を今週中に整えておくとよい。

- [Ultracode for Codex: Claude-style Dynamic Workflows with a Skill（dev.to）](https://dev.to/pablonax/ultracode-for-codex-claude-style-dynamic-workflows-with-a-skill-3knk) — v2.1.160 で確定した `ultracode` キーワードの使い方を解説した記事。`/effort ultracode` を設定すると Claude が毎回サブエージェントへの分散計画を立ててから実装に入るため、UIKit → SwiftUI の大規模移行など多ファイルをまたぐリファクタリングで特に威力を発揮する。

## 💡 今日のおすすめ実践 Tip

**XcodeBuildMCP v2.6 の `batch` ＋ `snapshot_ui` で UI テストのトークン消費を抑える**

v2.6.0 の最大の変化は、UI 操作のたびに画面全体を再スキャンしていた旧来の動作が、`snapshot_ui` で取得した「安定した要素参照（ラベル・アクセシビリティ ID ベース）」を再利用できる仕組みに変わった点です。

**Before（v2.5 以前）:**

```
tap ログインボタン   → 全スキャン（高トークン）
tap メール入力欄    → 再スキャン
tap 送信ボタン      → 再スキャン
```

**After（v2.6 `batch` 使用）:**

```
snapshot_ui → 全要素参照を一括取得（1 回のみ）
batch([tap ref:A, tap ref:B, tap ref:C]) → 参照を再利用して 1 呼び出し
```

Claude Code への指示例:

```
XcodeBuildMCP の snapshot_ui で現在の画面要素をすべて取得し、
「メールアドレス入力欄への入力」→「パスワード入力欄への入力」→「送信ボタンのタップ」を
batch ツールで 1 回の呼び出しにまとめて実行してください。
```

来週 WWDC で iOS 27 の新 UI コンポーネントが公開されたら、同じアプローチで UI テストを迅速に拡張できます。`wait_for_ui` を組み合わせてアニメーション完了を待機する処理も一緒に組み込んでおくと、Beta テスト期間の反復サイクルが大幅に短縮されます。
