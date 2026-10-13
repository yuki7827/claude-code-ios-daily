---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-10-14"
date: 2026-10-14 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- 該当なし（2026-10-14 時点で v2.1.296 が最新。直近リリースは 2026-10-11 キャッチアップ済み）

## 🛠 GitHub の動き

- **[Issue #89826（2026-08-26 作成 / OPEN・stale）— iOS Simulator コントロールツールが複数セッション同時実行時に指定デバイス UDID を無視する](https://github.com/anthropics/claude-code/issues/89826)** — 同一 Mac で複数の Claude Code セッションを並行実行した場合、各セッションが `UDID` を明示指定していても全シミュレーター操作が同じ（最後に起動した）デバイスに向いてしまうバグ。GitHub Actions self-hosted ランナーで複数のフィーチャーブランチを同時にテストするワークフローや、TCA / SwiftUI のモジュールテストを並列化している CI パイプラインに影響する。作成から約 7 週間が経過し `stale` ラベルが付いたが、OPEN のまま継続。公式修正が出るまでは「セッションごとに専用 Mac ランナーを割り当てる」か「後述の排他ロック戦略」が有効な回避策。ラベル: `bug`, `has repro`, `platform:macos`, `area:tools`, `stale`。

## 📝 日本語コミュニティ

- 該当なし（2026-10-14 時点で Zenn / Qiita に iOS 文脈の新規記事は確認できず）

## 🌐 海外コミュニティ / Tips

- **[10 Tips to Maximize Claude Code for iOS Development（Medium: CodeToDeploy）](https://medium.com/codetodeploy/10-tips-to-maximize-claude-code-for-ios-development-29027edc51a7)** — iOS 開発で Claude Code を最大限に使うための実践 Tips 集。「それを使っているのにうまくいかない開発者はクロードを高性能オートコンプリートとして使っているだけ」という切り口で、Swift・UIKit・SwiftUI の深い理解が Claude Code の出力品質に直結すると主張。プロジェクトの文脈ファイル（CLAUDE.md）の整備、XcodeBuildMCP と Apple 純正 Xcode MCP の併用、小さく reviewable なコミット単位での作業などを具体的に推奨。API キーのハードコードや deprecated な SwiftUI modifier の混入といった生成コードのリスクについての注意喚起も。

- **[Claude Code Skills for Server-Side Swift & Xcode Cloud — 15 open-source skill files for Vapor, Hummingbird, and CI/CD（Swift Forums: thread 85851）](https://forums.swift.org/t/claude-code-skills-for-server-side-swift-xcode-cloud-15-open-source-skill-files-for-vapor-hummingbird-and-ci-cd/85851)** — 「サーバーサイド Swift 向けの Claude Code スキルが一切なかった」という動機から、Vapor / Hummingbird / Fluent 対応の SKILL.md ファイルを 15 本まとめてオープンソース公開した Swift Forums のアナウンス投稿。Xcode Cloud との CI/CD 統合用スキルも含まれており、GitHub の `claude-code-server-side-swift-skills` リポジトリで入手できる。iOS バックエンドを Swift で構築しているチームがフロントエンドと同じ Claude Code ワークフローに統一できる可能性がある。コミュニティ製のため品質はまちまちだが、PR・Issue・フォーラム返信でのフィードバックを募集中。

## 💡 今日のおすすめ実践 Tip

**Issue #89826 対策 — CI で複数 Claude Code セッションが同一 Mac を共有する場合の iOS Simulator 排他ロック**

複数の Claude Code エージェントを self-hosted Mac ランナーで並行実行すると、Issue #89826 の影響で全セッションのシミュレーター操作が同じデバイスに向いてしまう。公式修正が出るまでの間、ファイルロックを使って「セッションごとに専用デバイスを確保する」排他戦略が有効。

```bash
#!/bin/bash
# .claude/hooks/acquire-simulator.sh
# PreToolUse フックとして登録し、xcodebuild / simctl 実行前にロックを取得する

LOCK_DIR="${TMPDIR:-/tmp}/claude-sim-locks"
mkdir -p "$LOCK_DIR"

# 利用可能な iPhone Simulator を探してロックを取る
for udid in $(xcrun simctl list devices available -j \
    | jq -r '.devices | to_entries[] | .value[] | select(.name | test("iPhone")) | .udid'); do

  LOCK_FILE="$LOCK_DIR/$udid.lock"
  # flock で排他ロック（取れなければ次のデバイスへ）
  exec 9>"$LOCK_FILE"
  if flock --nonblock 9; then
    echo "ACQUIRED_UDID=$udid"
    export SIMULATOR_UDID="$udid"
    break
  fi
done

if [ -z "$SIMULATOR_UDID" ]; then
  echo "ERROR: 利用可能なシミュレーターが見つかりません" >&2
  exit 1
fi
```

`.claude/settings.json` でフックを登録：

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "bash .claude/hooks/acquire-simulator.sh",
            "timeout": 30000,
            "onFailure": "block"
          }
        ]
      }
    ]
  }
}
```

CLAUDE.md に `シミュレーター操作時は必ず $SIMULATOR_UDID を使うこと` と明示しておくと、Claude Code が xcodebuild / simctl コマンドに `-destination 'id=${SIMULATOR_UDID}'` を自動的に付けるようになる。`flock` はセッション終了時にファイルディスクリプタが解放されるため、クラッシュ時の後始末も不要で CI に組み込みやすい。
