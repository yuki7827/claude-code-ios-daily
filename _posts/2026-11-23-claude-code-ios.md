---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-11-23"
date: 2026-11-23 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [v2.1.330（2026-11-20）](https://www.havoptic.com/r/claude-code-2.1.330) — **`/loop` スケジュール実行の上限が 7 日から 30 日に延長**。新ツール `easel` による自動チャート描画（Pro/Max/Team/Enterprise、macOS・Linux 対応）が追加。自己ホストランナー向けに `--start-dir` オプションが追加され、特定リポジトリディレクトリから作業開始できる。**iOS 関連の修正として Remote Control モバイルアプリで Diff・PR ボタンが非表示になる問題が解消**。長時間会話の compaction 後にフリーズする問題・WebFetch ハング・並列セッションでの設定消失・別セッション中の worktree 削除といった安定性バグも多数修正。iOS CI で `/loop` を組み合わせた夜間自動タスクを運用している場合、30 日対応で月 1 回のスケジュールも設定可能になった。

## 🛠 GitHub の動き

- 該当なし（本日時点で直近 5 投稿と重複しない iOS 関連の新規 GitHub 動向は確認できず）

## 📝 日本語コミュニティ

- [Claude Code × iOSアプリ開発：マルチエージェントで「ナンバーズ3追加・吉日カレンダー・ストアスクショ全自動生成」を1ビルドにまとめた話（Qiita / sorabcjanne1）](https://qiita.com/sorabcjanne1/items/5e91ea2a1588c2845e21) — 個人開発の iOS 数字くじアプリにメインセッション＋複数サブエージェント構成でロト6/7繰越表示・ナンバーズ3・吉日カレンダー・ストアスクショ全自動生成の 4 機能を 1 ビルドに同時追加した実践記録（2026-11-21）。タスクごとに Markdown 指示書を作りエージェントに渡す方式で、変更禁止スコープを明記しながら進行。ハマりどころとして SwiftUI の親コンテナ識別子による子要素の隠蔽、Linux Swift コンパイラの長い式の型検査エラー、Duo デバイスの撮影（開いた状態で真っ黒）、StoreKit テストが本番価格を参照する問題を詳述。ASC CLI の Duo 未対応についても API 直叩きで回避した手順がまとまっており、マルチエージェント × App Store 自動化の最先端事例。

## 🌐 海外コミュニティ / Tips

- [Building iOS Apps with AI Agents: The Practitioner's Guide（blakecrosley.com）](https://blakecrosley.com/guides/ios-agent-development) — 8 本の iOS アプリを Claude Code CLI でリリースした Blake Crosley によるリファレンスガイド（最終更新 2026-11-11）。Claude Code CLI・Codex CLI・Xcode ネイティブエージェントの 3 つの実行環境を比較し、MCP サーバー接続・CLAUDE.md テンプレート・Hooks 設定まで一通りカバー。最重要ルールとして **「エージェントに `.pbxproj` / `.xcodeproj/` を直接編集させない」** を挙げており、`PreToolUse` フックでブロックするサンプルコードを掲載。iOS 27 フレームワークはモデルの学習データに含まれていない可能性があるため、CLAUDE.md か都度のプロンプトで明示する必要があることも指摘。エージェントが苦手な領域（コード署名・ビジュアルレイアウト確認・Metal シェーダーデバッグ・パフォーマンスチューニング）を事前に把握して人間が担当する役割分担の考え方も参考になる。

## 💡 今日のおすすめ実践 Tip

**`PreToolUse` フックで `.pbxproj` 編集を禁止し、プロジェクトファイル破損を防ぐ**

Claude Code が Xcode プロジェクトファイル（`.pbxproj`）を直接書き換えると、ターゲット参照の破損やビルドフェーズの消失といった取り返しのつきにくいエラーが起きやすい。blakecrosley.com のガイドが「最重要ルール」と位置づけるのが、`PreToolUse` フックでこれを強制ブロックする設定だ。

`.claude/settings.json` に以下を追記するだけで有効になる:

```jsonc
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Edit|Write|MultiEdit",
        "hooks": [
          {
            "type": "command",
            "command": "bash -c 'echo \"$CLAUDE_TOOL_INPUT\" | python3 -c \"import json,sys; inp=json.load(sys.stdin); path=inp.get(\\\"file_path\\\",\\\"\\\"); exit(1 if any(p in path for p in [\\\".pbxproj\\\",\\\".xcodeproj/\\\"]) else 0)\"'",
            "on_failure": "block"
          }
        ]
      }
    ]
  }
}
```

プロジェクト構成の変更（新規ターゲット追加・ファイルのグループ移動など）が必要な場合は、Claude Code に Xcode の `xcodebuild` コマンドや xcodeproj gem 経由のスクリプトで操作させるか、人手で Xcode GUI を使う。エージェントがテキストエディタとして `.pbxproj` を書き換えようとする事故をこのフック 1 本で防げるため、MobileBuildMCP との組み合わせでも特に恩恵が大きい。
