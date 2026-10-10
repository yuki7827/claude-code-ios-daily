---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-10-11"
date: 2026-10-11 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- **[v2.1.296（2026-10-09）リリース](https://code.claude.com/docs/en/changelog)** — **サブエージェントに `autoCompactWindow` を追加**。これまでセッション全体で共通だったコンテキスト自動圧縮のウィンドウサイズを、サブエージェントごとに個別設定できるようになった。iOS プロジェクトで「ビルドログ解析専用サブエージェントは小さいウィンドウで高頻度圧縮」「UI テスト解析サブエージェントは大きいウィンドウで保持」といった細かいチューニングが可能に。また **`CLAUDE_CODE_OVERLOADED_RETRY_MAX_DELAY_MS` 環境変数を追加**し、API 過負荷時のリトライ上限ディレイを制御可能に（CI/CD ランナーでのタイムアウト設定と組み合わせて有効）。**Sonnet 5.5 のキャッシュリード価格が $0.10/M トークン** に確定。大型 Swift プロジェクトで繰り返し参照されるコンテキスト（`CLAUDE.md` やアーキテクチャドキュメント）のコスト試算が明確化された。

- **[v2.1.295（2026-10-08）リリース](https://code.claude.com/docs/en/changelog)** — **コマンドフックおよび HTTP フックに `onFailure: "block"` を追加**。フック自体が失敗（タイムアウト・エラー終了等）した際にツール実行をブロックするかどうかを設定できるようになった。iOS CI/CD では `xcodebuild` 実行前に事前チェックフックが失敗した場合に自動でビルドを止める安全弁として活用できる。**Program Status Protocol（OSC 7501）をサポート** — ターミナルがカーソル位置の報告に対応している場合にプロンプトの行数計算が正確になり、長い Swift エラーメッセージの表示が崩れにくくなる副次的効果も見込める。**`CLAUDE_CODE_RETRY_WATCHDOG_MAX_WAIT_MS` 環境変数を追加**し、リトライウォッチドッグの最大待機時間を制御可能に。

- **[v2.1.294（2026-10-08）リリース](https://code.claude.com/docs/en/changelog)** — **`prompt` および `agent` フックが命令として書かれていた場合にブロックすべきアクションを許可できていたセキュリティバグを修正**。hooks を iOS CI パイプラインの入口ガードとして使っている場合（危険な `simctl erase all` や `xcrun altool` を防ぐフック等）は即アップデート推奨。`Stop` および `SubagentStop` プロンプトフックの判定ロジックも改善され、誤検知によってセッションが強制終了されるケースが減少。

## 🛠 GitHub の動き

- 該当なし（2026-10-11 時点で新規 iOS / Xcode / Swift 関連 Issue の追加・クローズは確認できず）

## 📝 日本語コミュニティ

- 該当なし（2026-10-11 時点で Zenn / Qiita に iOS 文脈の新規記事は確認できず）

## 🌐 海外コミュニティ / Tips

- **[Best Swift / iOS Skills for Claude Code（2026）— claudeskills.info](https://claudeskills.info/best/swift-ios-skills/)** — Claude Code の Swift / iOS 向けスキルをインストール数で順位付けしたコミュニティランキング（2026年版）。1位は `xcode-project-setup`（`.pbxproj` を安全に編集して Swift Package を追加するスキル）、以下 SwiftUI HIG パターン、Swift 6 Concurrency 移行、iOS 26 Liquid Glass 対応、SwiftUI アニメーション最適化などが続く。XcodeBuildMCP の正規リポジトリ移転（`cameroncooke/XcodeBuildMCP` → `getsentry/XcodeBuildMCP`）に伴い、スキル側のセットアップコマンドも更新されているため、既存環境の確認を推奨。

## 💡 今日のおすすめ実践 Tip

**v2.1.295 の `onFailure: "block"` フックで iOS ビルド前チェックをより堅牢に**

v2.1.295 から、コマンドフックに `onFailure: "block"` を設定できるようになった。これまでは PreToolUse フックが内部エラーで失敗した場合でもツールは実行され続けていたが、`onFailure: "block"` を指定することでフック自体の失敗時にも即座にブロックできるようになる。

```json
// .claude/settings.json の設定例
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "bash .claude/hooks/check-ios-safety.sh \"$CLAUDE_TOOL_INPUT\"",
            "timeout": 10000,
            "onFailure": "block"
          }
        ]
      }
    ]
  }
}
```

```bash
# .claude/hooks/check-ios-safety.sh
#!/bin/bash
INPUT="$1"

# 危険なシミュレーター全消去を防ぐ
if echo "$INPUT" | grep -q "simctl erase all"; then
  echo "ERROR: 'simctl erase all' は禁止されています。特定のデバイス ID を指定してください。" >&2
  exit 2  # PreToolUse でのブロック
fi

# ビルド前提条件チェック（このスクリプト自体のエラーもブロックに）
if ! xcode-select -p &>/dev/null; then
  echo "ERROR: Xcode コマンドラインツールが見つかりません。" >&2
  exit 1  # onFailure: "block" で、このスクリプトの失敗もブロックとして扱われる
fi
```

以前は `check-ios-safety.sh` が Xcode 未インストール等の理由で `exit 1` した場合、フックの「失敗」としてスルーされてしまうことがあった。`onFailure: "block"` を加えることで「ガード自体が動かない = 実行禁止」の一貫した安全設計になる。CI ランナーで環境が不完全な状態での誤ビルドを防ぐのに特に有効。
