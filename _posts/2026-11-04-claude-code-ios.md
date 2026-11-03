---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-11-04"
date: 2026-11-04 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [v2.1.317（2026-11-03）](https://code.claude.com/docs/en/changelog) — 昨日リリース。iOS 開発・CI/CD に関連する主な変更点: ① **Agent SDK に `onFailure: "block"` オプションを追加**（hook callback が失敗した場合にアクション自体をブロックできる。ビルド失敗時に次の Write / Edit を止める保護ロジックの実装が容易になる）。② **`CLAUDE_CODE_DISABLE_NESTED_SESSION_ID=1` を追加**（`claude -p` をネストして呼び出す self-hosted runner 向け。Xcode Cloud や GitHub Actions で Claude Code のサブプロセスを呼ぶ構成で旧来の session-ID 継承挙動を維持できる）。③ **`claude -p` がディスクフル / クォータ超過時に exit 1 を返すように修正**（exit 0 だったためCIスクリプトで失敗が無視されていた問題を解消）。④ 実行中のバックグラウンドエージェントへの SendMessage 配信が高速化（マルチエージェント iOS ビルドループの応答性が向上）。⑤ Read ツールが同一ファイルの再読み込みでキャッシュスタブを返さずに実際の内容を返すように変更。

## 🛠 GitHub の動き

- [#96941（2026-09-25 open → 2026-11-03 更新・担当者アサイン済み）iOS Simulator パネルが iPhone Duo の折りたたみ状態・横向きを無視しスクリーンショットが captureFailed になる](https://github.com/anthropics/claude-code/issues/96941) — Claude Desktop の iOS Simulator パネルと `mcp__Claude_Code_iOS_Simulator__control` ツールが、iPhone Duo（折りたたみ型シミュレーター）の展開・横向き状態を認識しない問題。`attach` が折りたたみ時の座標空間（466×678pt）を返し続け、`screenshot` が `captureFailed` を返す。`xcrun simctl io <udid> screenshot` は正常に取得できるため暫定回避策として使える。Anthropic メンバーがアサインされ調査中。ラベル: `bug`, `platform:macos`, `area:desktop`。

- [#104749（2026-10-24 open → 2026-11-03 更新）Remote Control: `/rename` および `/remote-control <name>` が iPhone の Claude アプリ側のセッション名を更新しない](https://github.com/anthropics/claude-code/issues/104749) — Windows + VS Code で Claude Code を起動し、iPhone の Claude アプリからリモートコントロールする構成で、`/rename` コマンドを実行してもターミナルの `/status` には新名が反映されるが iPhone アプリ側は古い名前のまま変わらない。`/remote-control <name>` で渡した名前も別の自動名になるという報告。ドキュメント記載の優先順（`/remote-control` の引数 > `/rename`）が機能していない状態。ラベル: `bug`, `platform:ios`, `area:cli`。

## 📝 日本語コミュニティ

- 該当なし（2026-11-04 時点で、直近 5 件の記事に未掲載の新規 Zenn / Qiita 記事は確認できず）

## 🌐 海外コミュニティ / Tips

- [PreToolUse フックで `xcodebuild test` / `xcrun simctl` の直接呼び出しをブロックする（GitHub / ioloro/ios-testing）](https://github.com/ioloro/ios-testing) — Claude Code が `xcodebuild test` や `xcrun simctl` を生で呼び出すのを PreToolUse フックで検知・拒否し、MobileBuildMCP 経由の呼び出しに統一させる構成のプラグインプロジェクト。Xcode 14+・Swift 6 以上が前提で、`settings.json` に 2 行追加するだけで導入できる。「Claude が思ったより積極的に xcodebuild を直接叩いてくる」という問題への実践的な解決策。

- [PostToolUse フックで `.swift` 編集後に自動 swiftformat & ビルドチェックを走らせる Axiom の hooks 設定（charleswiltgen.github.io/Axiom）](https://charleswiltgen.github.io/Axiom/hooks/) — Claude Code が `.swift` ファイルを Edit / Write するたびに swiftformat を自動実行し、フォーマット差分があれば Claude に通知する PostToolUse フック設定を公開。同フックで `swift build` を走らせてコンパイルエラーを即座にフィードバックする設定例も含まれており、Swift コーディングの品質維持ループを CLAUDE.md だけでなくフックレイヤーで担保する考え方を整理している。

## 💡 今日のおすすめ実践 Tip

**v2.1.317 の `onFailure: "block"` フックを iOS CI ループに組み込む**

v2.1.317 で追加された `onFailure: "block"` オプションを使うと、hook callback が失敗した場合に後続のファイル操作をそのままブロックできる。これを iOS の CI ループに組み込む例：

```json
// settings.json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "swift build 2>&1 | head -40",
            "onFailure": "block"
          }
        ]
      }
    ]
  }
}
```

`onFailure: "block"` を指定することで、`swift build` が失敗したときに Claude が次の Edit を実行する前に止まり、ビルドエラーのログを受け取って自律的に修正を試みる。`onFailure: "continue"`（以前の唯一の選択肢）だと失敗を無視して次の操作に進むため、エラーが積み重なりやすかった。

XcodeBuildMCP（MobileBuildMCP）経由でビルドを走らせる場合は `xcodebuild` ラッパーを同様に指定すればよい。ただしビルド時間が長いプロジェクトでは every-edit フックが重くなるため、`"matcher": "Write(*.swift)"` のように拡張子フィルタで `.swift` ファイルに限定するのが実用的。
