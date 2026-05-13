---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-05-14"
date: 2026-05-14 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Claude Code v2.1.139 (May 11)](https://code.claude.com/docs/en/changelog) — **Agent View（Research Preview）** と **`/goal` コマンド** が目玉。`claude agents` で全セッション（実行中／待機中／完了）を一覧表示し、`/bg` で現在セッションをバックグラウンドに押しやりながら別のエージェントを操作できる。`/goal` は完了条件を書くと条件が満たされるまで Claude が自律的にターンを重ねる仕組みで、ビルド→テスト→修正のループを「監視なし」で回し続けることが可能に。`claude plugin details <name>` でプラグインのコンポーネント一覧とセッション当たりの推定トークンコストが確認できるようになり、iOS プロジェクトでのコンテキスト予算管理が容易に。`PostToolUse` フックの `continueOnBlock` 設定（拒否理由を Claude にフィードバック）や、フック `args: string[]` でシェルラッパーなし直接実行なども追加。
- [Claude Code v2.1.140 (May 12)](https://code.claude.com/docs/en/changelog) — `Agent` ツールの `subagent_type` 指定が大文字・区切り文字を問わず解決されるようになった（`"Code Reviewer"` → `code-reviewer` など）。`disableAllHooks` や `allowManagedHooksOnly` が設定されているときに `/goal` が黙って止まるバグを修正。シンボリックリンクで管理している `~/.claude/settings.json` 編集がホットリロードで拾われない不具合も解消され、チーム共有設定を Dotfiles で管理している iOS チームに影響。

## 🛠 GitHub の動き

- [twostraws/SwiftData-Agent-Skill](https://github.com/twostraws/SwiftData-Agent-Skill) — Paul Hudson（Hacking with Swift）による SwiftData 専用エージェントスキル「SwiftData Pro」。`@Model`・`@Query`・Predicate・マイグレーション・iCloud Sync・リレーションシップなど LLM が誤りやすいポイントを集中的にカバー。Claude Code / Codex / Cursor / Gemini など複数エージェントで使用可能。`/plugin marketplace add twostraws/SwiftData-Agent-Skill` で追加。
- [twostraws/Swift-Concurrency-Agent-Skill](https://github.com/twostraws/Swift-Concurrency-Agent-Skill) — 同じく Paul Hudson による「Swift Concurrency Pro」。Swift 6 の厳格な Concurrency チェックに対応したコード生成と、コンパイルエラー修正を支援。`Actor`・`async/await`・`@MainActor` 制約・`Sendable` 準拠など、Xcode 26 で Swift 6.3 をターゲットにした際に頻出する落とし穴を網羅。`/plugin marketplace add twostraws/Swift-Concurrency-Agent-Skill` で追加。

## 📝 日本語コミュニティ

- [Claude Codeでチケット駆動開発！JiraとMCP連携させて簡単なiOSアプリを作ってみました](https://zenn.dev/kobashuu/articles/64080a899cb3e1) (kobashuu, Zenn) — Jira の MCP サーバーと Claude Code を接続し、チケット内容を読んだ上でブランチ作成→SwiftUI 実装→テストまで一連で流すワークフローを実験した記録。チームの iOS 開発でチケット→実装の橋渡しを AI に任せる手法の実践例として参考になる。
- [個人開発で使ってる Claude Code の自作スキル](https://zenn.dev/hiraly/articles/b2d57af077ece1) (hiraly, Zenn) — Swift/SwiftUI での iOS ネイティブ開発を主軸に置く著者が、繰り返し使う操作をスキルに切り出した実録。プロジェクト固有の命名規則・コーディング規約を埋め込んだカスタムスキルを CLAUDE.md と組み合わせる構成を紹介。自作スキル作成のとっかかりとして参考になる。

## 🌐 海外コミュニティ / Tips

- [Anthropic adds Agent View to Claude Code CLI interface](https://www.testingcatalog.com/anthropic-adds-agent-view-for-claude-code-for-parralel-work/) (Testing Catalog) — Agent View が並行エージェント管理にどう寄与するかをまとめた解説。各行にセッション・状態・最終アクティビティを表示し「どのエージェントが自分の承認待ちか」を一目で把握できる設計。iOS の機能ブランチを worktree で分割して複数 Claude セッションを走らせる構成との相性が良い点を指摘。
- [I Tried Claude Code Agent View (New Way to See Your Agents WORKING)](https://medium.com/@joe.njenga/i-tried-claude-code-agent-view-new-way-to-see-your-agents-working-e8c132aea112) (Joe Njenga, Medium, May 2026) — `claude agents` を実際に試した感想レポート。`/bg` でバックグラウンドに送ったセッションが Agent View に列挙される流れと、`claude --bg "<task>"` でフォアグラウンドを開かずに新規セッションを起動する方法を具体的に解説。iOS の UI 実装・ビジネスロジック・テストを別セッションに分けて同時進行させる際の操作感を紹介。

## 💡 今日のおすすめ実践 Tip

**`/goal` + XcodeBuildMCP で「ビルド→テスト→修正」を自律ループ化する**

v2.1.139 で追加された `/goal` は、完了条件を満たすまで Claude が自動でターンを重ねます。XcodeBuildMCP と組み合わせると、iOS 開発で最も面倒な「失敗→原因調査→修正→再ビルド」のループをほぼノーハンドで回せます。

```
# Claude への指示例
/goal TaskListView の Swift Testing テストがすべて GREEN になるまでビルドと修正を繰り返してください。
XcodeBuildMCP の build_and_test ツールを使い、失敗したテストの原因を特定して修正してから
再実行してください。10 回試みても通らない場合は止まって報告してください。
```

ポイントは「停止条件」を明記することです（例：「10 回試みても通らない場合は止まれ」）。これを書かないと Claude が無限ループに入る可能性があります。さらに `claude --bg "/goal ..."` としてバックグラウンドセッションで流しつつ、別フィーチャーの実装をフォアグラウンドの Claude で進める構成にすると、Agent View で両セッションの状態を俯瞰しながら iOS 開発を効率化できます。`continueOnBlock` フック設定と組み合わせると、拒否した操作の理由を Claude に自動フィードバックしてリカバリー速度も向上します。
