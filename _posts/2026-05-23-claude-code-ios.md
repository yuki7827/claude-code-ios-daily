---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-05-23"
date: 2026-05-23 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Claude Code v2.1.148 (May 22)](https://code.claude.com/docs/en/changelog) — v2.1.147 で導入されたリグレッションを緊急修正。**Bash ツールが全コマンドで exit code 127 を返す**バグが一部ユーザーに発生しており、`xcodebuild`・`xcrun`・`swift build`・`fastlane` など iOS 開発で使う CLI が一切実行できなくなっていた。v2.1.148 へのアップデートで解消。影響を受けていた場合は `claude --version` で確認し `npm update -g @anthropic-ai/claude-code` で更新する。

## 🛠 GitHub の動き

- [XcodeBuildMCP v2.5.2 (May 12)](https://github.com/getsentry/XcodeBuildMCP/releases) — バンドル済み AXe バイナリを **v1.7.0** に更新（UI automation・スクリーンショット・ビデオ録画の改善）。`log-capture` の**セキュリティ脆弱性**を修正（細工した Bundle ID でシミュレータのログストリームが意図せず拡大されるリスクを遮断）。`debug_attach_sim` ツールが明示的に指定した PID で Bundle ID のデフォルト値を上書きできるよう修正。最新版への更新は `brew upgrade xcodebuildmcp` で可能。

- [Issue #59172 — Claude Agent が Xcode Intelligence に接続できない（Open、May 14）](https://github.com/anthropics/claude-code/issues/59172) — Xcode Intelligence の「Agents」タブで Claude Code をサインインしても **「not logged in」のまま更新されない**認証バグ。Claude Chat（Chat タブ）は正常に Xcode と接続できるが、Claude Code Agent 側のみ認証状態が同期されない。原因は Agent タブ固有の認証状態同期問題と推測。回避策は Claude Code CLI をターミナルから直接使うか、claude.ai の Chat セッションと Xcode を組み合わせる。

## 📝 日本語コミュニティ

- [SwiftData × Claude Code で永続化層を設計する — @Model設計からマイグレーションまで実務で詰まらないための完全ガイド](https://qiita.com/kotaro_ai_lab/items/119901d60f34e07da801) (kotaro_ai_lab, Qiita) — SwiftData の永続化層設計を Claude Code と組み合わせた実践ガイド。LLM が起こしやすいミス（`@Model` クラスにデフォルト値なし、`deleteRule` 指定漏れ、スキーマ変更時の `VersionedSchema` 実装忘れ等）を整理したうえで、マイグレーション・リレーションシップ・iCloud 同期まで Claude Code と進めるフローを解説。SwiftData を新規採用する iOS プロジェクトへの事前チェックリストとして参照価値が高い。

## 🌐 海外コミュニティ / Tips

- [Agent skills in Xcode: How to install and use them today](https://www.hackingwithswift.com/articles/283/how-to-install-and-use-ai-agent-skills-in-xcode) (Paul Hudson, Hacking with Swift) — Xcode 26.3 のエージェント型コーディング環境に Paul Hudson 製の Swift 系 Agent Skill を導入する手順を解説。[twostraws/swift-agent-skills](https://github.com/twostraws/swift-agent-skills) が SwiftUI Pro・Swift Testing Pro・Swift Concurrency Pro・SwiftData Pro など 30+ スキルのキュレーションディレクトリとして機能しており、`npx skills add <URL> --skill <name>` 1 コマンドで Claude Code・Codex・Cursor・Xcode のいずれにも導入できる。プロジェクトスコープかグローバルスコープかを選ぶだけで、全 IDE に横断して有効化される仕組みを説明している。

## 💡 今日のおすすめ実践 Tip

**v2.1.148 に更新してから `xcodebuild` が動かない場合の確認手順**

v2.1.147 の Bash exit code 127 リグレッションは v2.1.148 で修正済みだが、アップデートが反映されていないと `xcodebuild build` や `xcrun simctl list` などがすべて失敗する。以下の順で確認・対処できる。

```bash
# 1. バージョン確認
claude --version
# → 2.1.148 未満なら更新が必要

# 2. 更新
npm update -g @anthropic-ai/claude-code

# 3. 更新後も失敗する場合は PATH を確認
# Claude Code のシェル環境では PATH が短縮されているケースがある
claude -p 'echo $PATH' 
# Xcode CLT のパス（/usr/bin, /usr/local/bin）が含まれているかを確認

# 4. CLAUDE.md にシェル初期化を明示する（根本対策）
# 例: プロジェクトの CLAUDE.md 冒頭に以下を追記
# ## Shell 環境
# すべての Bash コマンドは /bin/zsh -i -c '<cmd>' 形式で実行すること。
# PATH には /usr/bin:/usr/local/bin:/opt/homebrew/bin を含めること。
```

XcodeBuildMCP を使っている場合は `xcodebuildmcp --version` でバイナリも v2.5.2 以上になっているか合わせて確認するとよい。シミュレータの UI automation（AXe）で予期しない挙動が出ていた場合も v2.5.2 の AXe v1.7.0 更新で改善されている可能性がある。
