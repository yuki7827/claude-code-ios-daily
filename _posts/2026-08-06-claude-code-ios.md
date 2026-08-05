---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-08-06"
date: 2026-08-06 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- **v2.1.222（2026-08-04）リリース**（[Claude Code 変更ログ](https://code.claude.com/docs/en/changelog)）— バグ修正中心のリリース。iOS / XcodeBuildMCP ワークフローに関係する主な修正: ① **worktree 分離セッション・サブエージェントがメインチェックアウトへ destructive な git コマンドを実行できた問題を修正** — `git worktree` を使って feature ブランチごとに XcodeBuildMCP のビルドを分離している構成で、サブエージェントが意図せず main ブランチを上書きするリスクが解消; ② **PreToolUse auto-allow フックがバックグラウンドエージェントタスクでツール制限をバイパスしていた問題を修正** — XcodeBuildMCP のビルド/テストをバックグラウンドエージェントとして走らせている場合に、意図しないツール呼び出しが承認されていた問題の解消; ③ **`ultraplan` 機能を削除** — 計画モードの大幅改修。CLAUDE.md 等で `ultraplan` を呼び出しているワークフローがあれば `/plan` に書き換えを。

## 🛠 GitHub の動き

- [Issue #84295 — Mobile (iOS): spawn_task チップとサイドバーが Remote Control 経由でタップ不能（OPEN / 2026-08-05）](https://github.com/anthropics/claude-code/issues/84295) — iPhone の Code タブ（Remote Control）および Safari の `claude.ai/code` で **バックグラウンドタスク提案チップ（spawn_task chips）がタップに反応せず、セッションツリー（サイドバー）の項目も操作不能**という iOS 特有のバグ。報告者が DOM を精査した結果、spawn_task chips は `pointer-events: none` が設定されたコンテナ内にレンダリングされ、サイドバーの各セッション行は縦幅が **26px**（Apple の推奨最小タップターゲット 44px の 59%）しかなく 70 インタラクティブ要素中 69 個（98.6%）がタップターゲット未達という根本的な問題と判明。**回避策は「Safari → デスクトップ用 Web サイトを表示 + ピンチズームで側面パネルを拡大」または「spawn_task チップの代わりにチャットテキストで指示を入力」。** XcodeBuildMCP で複数ターゲットのビルドを並列に分岐させる spawn_task ワークフローを iPhone から制御しようとしている開発者に直撃する。

- [Issue #84028 — CLI と iOS アプリが同一ライブセッションで 60 秒差に異なるモデルを表示、課金ティアをまたぐためセッションコストが検証不能（OPEN / 2026-08-05）](https://github.com/anthropics/claude-code/issues/84028) — デスクトップ CLI の `/model` が「Sonnet 5」と表示している同一セッションを、iOS アプリで開くと「Opus 5」と表示されるケースが確認された。Sonnet と Opus はモデル料金単価が大きく異なるため、**どちらの課金が発生しているか確認する手段がない**という実害のあるバグ。別インスタンスでは「Fable 5（デスクトップ）vs Opus 4.8（iOS）」という差異も報告されており、後者はデスクトップのモデル選択リストに存在しないモデル名まで表示されている。iOS Remote Control から XcodeBuildMCP ビルドを回す際にどのモデルが実際に動いているか確認できないため、コスト管理が困難になる。

## 📝 日本語コミュニティ

- 該当なし（本日時点で直近 5 投稿（2026-08-01〜08-05）と重複しない新規の iOS / Swift / Xcode 関連記事は Zenn・Qiita ともに確認できなかった）

## 🌐 海外コミュニティ / Tips

- **[anthropics/ClaudeForFoundationModels（GitHub）](https://github.com/anthropics/ClaudeForFoundationModels)** — WWDC 2026（6/9）で発表された Anthropic 公式 Swift パッケージ。Apple Foundation Models フレームワークの `LanguageModel` プロトコルに Claude を適合させ、オンデバイスモデルと **同一の `LanguageModelSession` API** で Claude Sonnet 5 / Opus 5 を呼び出せる。依存切り替えは引数 1 つ（`ClaudeLanguageModel(name: .sonnet5, auth: .apiKey("..."))`）で完結し、`@Generable` による構造化出力・ストリーミング・ツール呼び出しもそのまま使える。認証は **App Attest**（デバイス紐付け、キー非同梱、本番向き）/ API キー（シミュレーター反復向き）/ プロキシ中継の 3 種類。対応プラットフォームは iOS 27 beta / macOS 27 beta / visionOS 27 beta / watchOS 27 beta。Apache-2.0、ベータ版で外部コントリビューション非受付。直近 5 投稿での掲載なし。[artemnovichkov.com — Using Claude with Apple Foundation Models（実装レポート）](https://artemnovichkov.com/blog/using-claude-with-apple-foundation-models)も参考に。

## 💡 今日のおすすめ実践 Tip

**「Issue #84295 の回避策: iPhone Remote Control で spawn_task チップが使えないとき、チャットテキストでサブエージェントを起動する代替フロー」**

Issue #84295 が示すように、現行の iPhone Remote Control（Code タブ / `claude.ai/code`）では spawn_task チップをタップできません。XcodeBuildMCP を使った iOS 開発でビルドターゲットごとにサブエージェントを並列起動したい場合、チップをタップする代わりに以下のテキスト指示でほぼ同等のことができます。

**CLAUDE.md に spawn_task の代替フレーズを事前登録する**

```markdown
## iPhone Remote Control からのタスク分岐フレーズ

以下のフレーズが届いたときは、指示通りにサブエージェントを起動すること:
- 「タスクA とタスクB を並列で実行」
  → spawn_task で 2 サブエージェントを起動 (worktree 分離)
- 「XcodeBuildMCP でターゲットX とターゲットY を同時ビルド」
  → 各ターゲットを別サブエージェントに割り当て
- 「バックグラウンドで〇〇しながら続けて」
  → spawn_task を使い現行会話を止めずにサイドタスクを開始
```

**チャットで直接サブエージェントを呼び出す例**

iPhone の Code タブのテキスト入力欄に（上記 CLAUDE.md がある場合）こう打ち込むだけで spawn_task 相当の効果が得られます:

```
「XcodeBuildMCP でターゲット MyApp とターゲット MyAppTests を同時ビルド」
```

**タップターゲット問題の暫定対処（サイドバー）**

サイドバーの各セッション行（26px 幅）が小さすぎてタップできない場合は:

1. Safari で `claude.ai/code` を開く
2. `AA` メニュー（アドレスバー左）→「デスクトップ用 Web サイトを表示」
3. 2本指ピンチアウトでサイドバー付近を拡大してからタップ

Issue のフォローを続け、修正が入り次第 Tip を更新します。
