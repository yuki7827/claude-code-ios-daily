---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-11-07"
date: 2026-11-07 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [v2.1.319（2026-11-06）](https://code.claude.com/docs/en/changelog) — 昨日リリース。iOS 開発・CI に関連する主な変更点: ① **MCP タスクのバックグラウンド実行が正式サポート**（`--tasks` 対応 MCP サーバーが実行するツール呼び出しをすぐにバックグラウンドへ移し、完了時に結果を報告するようになった。XcodeBuildMCP のビルド・テスト呼び出しが長時間かかっても Claude セッションをブロックしなくなり、ビルド待ちの間に別ファイルの修正を並行して進めるユースケースが実現）。② **`CLAUDE_CODE_DISABLE_MCP_TASK_BACKGROUND=1`** でバックグラウンド化を無効にしインラインで待機させる制御が可能。③ **`permissions.denyInsteadOfPrompting`** を追加（本来パーミッション確認が必要なツール呼び出しを「許可待ちでブロック」ではなく「拒否して続行」に変える設定。Xcode Cloud や GitHub Actions などのヘッドレス CI で Claude Code を走らせるとき、確認ダイアログで処理が止まるのを防ぐ）。④ **Claude iOS アプリのファイルカード未表示バグを修正**（Claude がファイルを 1 件送信した際にファイルカードが表示されないことがあった問題）。⑤ MCP サーバーの旧ハンドシェイク接続失敗・空レスポンスを修正（XcodeBuildMCP を含む古いプロトコル実装への影響を解消）。

## 🛠 GitHub の動き

- 該当なし（2026-11-07 時点で直近の iOS / Xcode / Swift 関連 Issue・PR は前回（11-06）掲載の #96941 / #104749 から更新なし）

## 📝 日本語コミュニティ

- 該当なし（2026-11-07 時点で、直近 5 件に未掲載の新規 Zenn / Qiita 記事は確認できず）

## 🌐 海外コミュニティ / Tips

- [MCP Tasks landed in Claude Code（dev.to / piekwerk）](https://dev.to/piekwerk/mcp-tasks-landed-in-claude-code-a-tool-call-can-now-outlive-your-session-and-6-config-decisions-2l5k) — v2.1.319 の MCP タスク機能を解説した記事。`CLAUDE_CODE_DISABLE_MCP_TASK_BACKGROUND=1` でインライン待機に戻す方法、`CLAUDE_CODE_DISABLE_MCP_TASKS=1` でタスク機能を完全無効化する方法を整理。iOS ビルドの MCP ツール呼び出しで実測 20〜30 秒かかっていたものがノンブロッキングになった報告も含まれる。バックグラウンドタスクのキャンセルは `/tasks` → `x` キーであること（Esc は不可）という注意点も記載。

- [AppTestCircuit — Claude Code plugin for iOS simulator loop（github.com/webcoyote）](https://github.com/webcoyote/AppTestCircuit) — ビルド → シミュレーター起動 → アクセシビリティ検査 → テスト → 修正 → 再テストのループを Claude Code で自動化するプラグイン。内部で `iosef`（iOS Simulator integration CLI）を自動インストールし、SwiftUI コントロールのタップ・スクロール・テキスト入力をアクセシビリティ ID 経由で実行する。設定ファイルは最小限で、`button`・`textField` などの UI 要素に `accessibilityIdentifier` を付与するだけで利用できる構成。Xcode インストール必須。

## 💡 今日のおすすめ実践 Tip

**`permissions.denyInsteadOfPrompting` で Xcode Cloud / GitHub Actions の CI ループを止めない**

v2.1.319 で追加された `permissions.denyInsteadOfPrompting` を使うと、パーミッション確認が必要なツール呼び出しを「待ちでブロック」ではなく「即拒否→続行」に変えられる。iOS CI での典型的な設定例：

```json
// .claude/settings.json（プロジェクト共有設定）
{
  "permissions": {
    "denyInsteadOfPrompting": true,
    "allow": [
      "Bash(xcodebuild:*)",
      "Bash(xcrun:*)",
      "Bash(swift:*)",
      "mcp__XcodeBuildMCP__*"
    ]
  }
}
```

`denyInsteadOfPrompting: true` にしたうえで `allow` リストを明示的に設定しておけば、許可済みツールはそのまま実行され、想定外の操作（例: `rm -rf` や外部ネットワーク送信）はブロックされて処理が続く。ヘッドレス CI でパーミッションダイアログに引っかかって処理が止まる問題を根本から解消できる。

ローカル開発では `denyInsteadOfPrompting` を外した設定ファイル（`settings.local.json`）を `.gitignore` に入れておくと、CI と手元の挙動を分けて管理できる。
