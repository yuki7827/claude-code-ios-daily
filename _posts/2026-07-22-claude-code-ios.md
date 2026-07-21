---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-07-22"
date: 2026-07-22 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Claude Code v2.1.216（2026-07-20）](https://github.com/anthropics/claude-code/releases/tag/v2.1.216) — iOS × XcodeBuildMCP 環境で効く変更が複数含まれる。**① `sandbox.filesystem.disabled` 設定追加**：ファイルシステム分離をスキップしながらネットワーク出力制御だけ維持する設定が追加された（詳細は Tips セクション参照）。**② 長期セッションの二次計算コスト修正**：メッセージ正規化がターン数に対して二次的に増加し、多セッション操作時に多秒単位の遅延とスロー・レジュームが起きていた問題を修正。Xcode ビルドを長時間走らせた後に Claude Code を resume すると重くなる症状が解消。**③ バックグラウンドセッションのエージェントプロンプト・ツール制限が resume 後に復元されるよう修正**：`claude agents` でバックグラウンドビルドを走らせ、中断して再開すると MCP ツール制限が消えてしまうバグを修正。**④ ワークツリー分離の git リダイレクト修正**：ワークツリー分離サブエージェントが `git -C` / `--git-dir` / `GIT_DIR` 経由で共有チェックアウトに書き込んでしまうバグを修正（XcodeBuildMCP を worktree isolation と組み合わせているチームに影響）。

## 🛠 GitHub の動き

- [Issue #79711 — iOS Safari でMCPツール承認後も `-32003: MCP tool call requires approval` が継続（OPEN / 2026-07-21）](https://github.com/anthropics/claude-code/issues/79711) — `claude.ai/code` をモバイル Safari（iOS）で開き、クラウドリモートセッション内の MCP ツール（`create_trigger` / `send_later` 等）を呼び出すと承認ダイアログが出る。「Allow」をタップしても `-32003` エラーが返り続け、`settings.local.json` の permissions allow リストに追加しても解消しないバグ。v2.1.216 の環境で報告されており、XcodeBuildMCP のツールをクラウドセッションから iPhone で承認しようとしても同様に詰まる可能性がある。暫定回避：`Bash` 等の組み込みツールは問題なく動作するため、MCP ツールを直接呼ぶのではなく `Bash` 経由で `xcodebuild` を実行する代替フローを取る。

- [Issue #79692 — Mac 上の Claude Code をモバイル+デスクトップから tmux/Remote Control の手間なしにシームレスに操作したい（OPEN / 2026-07-21）](https://github.com/anthropics/claude-code/issues/79692) — Mac mini 上で動く Claude Code セッションをラップトップと iPhone から seamlessly 共有する運用のために、~400 行のランブック（tmux 起動 → GUI ログインセッション必須 → RC ブリッジのサイレントドロップ対策…）を書かざるを得ないという現状を詳細に告発した Feature Request。「Codex は同一リポジトリのセッションをラップトップ・iPhone 双方から即座に共有できるのに Claude Code は tmux + Remote Control という Community-grade な橋渡しが必要」という比較が Anthropic への改善要望として添えられている。RC のサイレントドロップ・デスクトップとモバイルの接続状態表示の不一致・リモートからの再認証不可（`/login` が RC 上では動作しない）など、iOS 開発者が日常的に踏む問題が網羅的にまとめられており参考になる。

## 📝 日本語コミュニティ

- [XcodeBuildMCP × Claude Code スキルシステムで、iOS ビルドを自動化する（Zenn / kyoichi）](https://zenn.dev/kyoichi/articles/claude-code-xcodebuildmcp-ios-build) — Claude Code のスキルシステム（CLAUDE.md に登録する `/build-ios` 等のカスタムコマンド）と XcodeBuildMCP ツールを組み合わせ、`/build-ios`→ビルドエラー自動修正 → `/test-ios` → 失敗テストの修正まで 1 コマンドで回すフローを構築する方法を解説。スキルの引数として `projectPath` / `scheme` / `simulator` を渡すことで複数ターゲット（iPhone / iPad / Vision Pro）に対応する設計が特筆点。

- [Xcode MCP × Claude Code プラグインで、iOS ビルドを自動化する（Zenn / kyoichi）](https://zenn.dev/kyoichi/articles/claude-code-plugin-xcode-mcp-hybrid) — Apple 純正 Xcode MCP（`mcpbridge` で提供される `RenderPreview` / `AppleDocumentationSearch` / `ExecuteSnippet` の 3 ツール）と XcodeBuildMCP（`BuildProject` / `RunAllTests` / UI 操作系）を Claude Code プラグインとして共存させるハイブリッド構成を解説。「純正 Xcode MCP はビジュアル検証に、XcodeBuildMCP は CI 相当のビルド・テスト実行に使い分ける」というツール役割分担が実践的。上記スキルシステム記事の前段として合わせて読むと体系的に理解できる。

## 🌐 海外コミュニティ / Tips

- [Two MCP Servers Made Claude Code an iOS Build System（Blake Crosley / blakecrosley.com）](https://blakecrosley.com/blog/xcode-mcp-claude-code) — XcodeBuildMCP と Apple 純正 Xcode MCP の 2 サーバーを Claude Code に同時接続し、フル iOS ビルドシステムを構築した実践ブログ。「XcodeBuildMCP が 82 ツール（ビルド・テスト・シミュレーター・LLDB）、Xcode MCP が 20 ツール（ファイル操作・診断・SwiftUI プレビュー）を提供し、重複するツールは Claude Code が自動的に優先度判定する」という設計の実態を具体的に紹介。`.claude/settings.json` で各 MCP のスコープを分離し、`allowedTools` でどの MCP のどのツールを使うかを制御するサンプル設定も掲載。

## 💡 今日のおすすめ実践 Tip

**「v2.1.216 の `sandbox.filesystem.disabled` で XcodeBuildMCP の Permission エラーを解決する」**

Claude Code v2.1.216 で追加された `sandbox.filesystem.disabled` 設定は、XcodeBuildMCP を使う iOS 開発者には特に有用な変更です。

**背景：なぜ Permission エラーが起きるか**

Claude Code の macOS サンドボックス（`sandbox-exec`）はデフォルトでプロジェクトルート外へのファイルシステムアクセスを遮断します。XcodeBuildMCP が参照する以下のパスがプロジェクトルート外にある場合、`Operation not permitted` エラーが発生します。

- `~/Library/Developer/Xcode/DerivedData/` — ビルド成果物
- `/Applications/Xcode.app/` — Xcode 本体
- `~/.xcode/` や `/tmp/` — ビルドキャッシュ・テンポラリ

**v2.1.216 の解決策**

```json
// .claude/settings.json
{
  "sandbox": {
    "filesystem": {
      "disabled": true
    }
  }
}
```

この設定でファイルシステム分離をスキップしながら、**ネットワーク出力制御（egress フィルタ）は引き続き有効**な状態を維持できます。MCP ツールがビルド成果物を DerivedData に書き込んだり、Xcode ツールチェーンを呼び出したりする際に Permission が通るようになります。

**適用時の注意点**

| 項目 | 説明 |
|---|---|
| 対象スコープ | プロジェクト単位（`.claude/settings.json`）で設定推奨。グローバル設定は避ける |
| ネットワーク制御 | `disabled: true` でもネットワーク egress フィルタは有効なまま |
| 代替案（v2.1.215 以前） | `sandbox.allowedPaths` に DerivedData・Xcode のパスを列挙する方法も引き続き使用可能 |

**Issue #79711（iOS Safari での MCP 承認後 -32003 エラー）との関係**

このバグが修正されるまでの間、iPhone から claude.ai/code を開いて XcodeBuildMCP ツールを承認しようとしても失敗します。回避策として、Claude Code が MCP ツールの代わりに `Bash` で `xcodebuild` を直接実行するよう CLAUDE.md に指示を書いておく方法が有効です。

```markdown
## iOS ビルド実行方針
MCP ツール呼び出しが iOS Safari の承認ダイアログで失敗する場合は、
XcodeBuildMCP の代わりに Bash で直接 xcodebuild を呼んでください。

例：
```bash
xcodebuild build \
  -scheme MyApp \
  -destination 'platform=iOS Simulator,name=iPhone 16 Pro' \
  -configuration Debug \
  2>&1 | tail -20
```
```
