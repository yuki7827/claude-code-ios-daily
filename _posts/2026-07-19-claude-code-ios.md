---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-07-19"
date: 2026-07-19 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Claude Code v2.1.214（2026-07-18）](https://code.claude.com/docs/en/changelog) — セキュリティと権限管理の強化が中心。主な変更点は 5 つ。**① `EndConversation` ツールの追加**：Claude Code が MCP ツールとして `EndConversation` を公開し、乱用ユーザーや悪意あるコンテンツとの会話を Claude 側から終了できるようになった。**② Windows PowerShell 5.1 の権限チェックバイパス脆弱性修正**：ツール入力を通じて権限チェックを回避できた問題を修正。Mac メインの iOS 開発者には直接影響は少ないが、CI/Windows Server 経由で Claude Code を走らせている場合に重要。**③ Docker コマンドへの権限プロンプト追加**：`docker run / exec` など破壊的な Docker 操作に承認ダイアログが挟まるようになった。**④ 長時間実行ツール呼び出しの進捗ハートビート追加**：XcodeBuildMCP 経由の `BuildProject` など数分かかるツール呼び出し中に定期的な「まだ実行中」ハートビートが表示され、フリーズとの区別がしやすくなった（詳細は Tips 参照）。**⑤ Bash 権限チェックの複数改善**：ファイル・ディレクトリリダイレクト、単一セグメント `dir/**` 許可ルール、長いコマンドの処理精度を改善。

## 🛠 GitHub の動き

- [Issue #78883 — Mobile RC: iOS Remote Control でウィジェット・画像ツール出力が生テキストのみになる（OPEN・label:platform:ios / 2026-07-18）](https://github.com/anthropics/claude-code/issues/78883) — iOS アプリの Remote Control セッションで `show_widget` 視覚ツール（チャート・モックアップ・確認ウィジェット）を実行しても、出力が "Content rendered and shown to the user…" という生テキストにしか表示されない問題。XcodeBuildMCP でビルド結果のチャートや SwiftUI プレビュー画像を iPhone から確認したいユースケースで直接踏む。代替として Artifact を使う方法もあるが、サインイン済みブラウザへの追加ホップが必要になる。`platform:ios` ラベルが付いており Anthropic チームも認識している。

- [Issue #78878 — Remote Control ブリッジがサイレントダウン・自動再接続なし（アプリ完全再起動のみ回復）（OPEN / 2026-07-18）](https://github.com/anthropics/claude-code/issues/78878) — ローカルセッションの RC ブリッジが内部エラー（`Cannot read properties of undefined (reading 'session_url')`）でサイレントに死亡し、デスクトップアプリが開いたままでも自動再接続が行われない。`/remote-control` コマンドを打っても「この環境では使えない」と返すだけで根本エラーを表示しない。完全にアプリを落として再起動すれば復旧できるが、外出中に iPhone から気づくことが難しい。長時間 Xcode ビルドを走らせながら Remote Control で監視している構成で危険。暫定回避：ビルド完了後に Bark 等の push 通知 hook で即時通知を受け取り、RC ドロップを early detect する。

- [Issue #78538 — ShipIt 自動アップデーター：インストール後にデスクトップアプリを再起動しない（スケジュールタスク停止）（OPEN・5コメント / 2026-07-17）](https://github.com/anthropics/claude-code/issues/78538) — Claude Desktop の自動アップデーター（ShipIt）が更新インストール後に `launchAfterInstallation: false` を設定したままアプリを再起動しない問題。iOS 開発者がスケジュールタスク（例：毎朝 8:00 に XcodeBuildMCP で夜間ビルドを走らせる構成）を持っている場合、更新タイミング（報告事例では毎朝 7:15 頃）にアプリが停止し、スケジュールが実行されず気づかないケースが続発している。根本修正として `launchAfterInstallation: true` に変更するか、「アップデート後に自動再起動する」設定を追加することが求められている。暫定回避：`launchd` で Mac 起動時に Claude Desktop を自動再起動する `plist` を登録する。

## 📝 日本語コミュニティ

- [【第1弾】VS CodeのClaude Codeで「iOSコードレビュー → 修正 → Xcodeビルド確認」を自動化する（Qiita / 4q_sano）](https://qiita.com/4q_sano/items/f29dba5fa92958850a32) — VS Code 上の Claude Code から MCP 経由（`xcrun mcpbridge`）で Xcode に直接つなぎ、コードレビュー → 自動修正 → `xcodebuild` ビルド確認までを 1 ループにする手順を初心者向けに解説。専用の Xcode MCP bridge を使うことで、Claude Code が Xcode のビルドエラーを直接受け取って修正できる点が差別化ポイント。

- [【第2弾】VS CodeのClaude Codeで「iOSコードレビュー → 修正 → Xcodeビルド確認 → テスト項目作成」を自動化する（Qiita / 4q_sano）](https://qiita.com/4q_sano/items/effe51de5e9654777f1e) — 第1弾のビルド確認に加えて「XCTest テスト項目の自動生成」までループに組み込んだ第2弾。Claude Code が修正コードに対して XCTest のユニットテストケースを自動で書き、`xcodebuild test` で実行して結果を確認する一連のフローを示している。

- [Xcode 26.3 エージェント型コーディング入門 — Claude・Codex・MCPで開発を自動化（Qiita / kai_kou）](https://qiita.com/kai_kou/items/1b24ad62dde4c02ae4f0) — Xcode 26.3 に統合された Claude Agent・Codex・MCP サーバー（`RenderPreview`・`ExecuteSnippet`）を使ってエージェント型コーディングを始めるための入門記事。macOS Tahoe 以降・Apple Silicon 必須の制約や、Xcode Agent と Claude Code CLI の使い分け、MCP でカスタムツールを追加する方法まで体系的にまとめている。

## 🌐 海外コミュニティ / Tips

- [AgentHub — iOS シミュレーターライブプレビュー内蔵の Claude Code セッションマネージャー（jamesrochabrun / macOS）](https://github.com/jamesrochabrun/AgentHub) — Claude Code の全セッションをネイティブ macOS UI で一元管理するアプリ。複数 worktree を並列ターミナルで実行し、差分をインラインで確認・編集できる。特筆すべきは **iOS シミュレーターのライブミラーリング機能**：ブートされたシミュレーターを AgentHub 内にミラー表示しながら UI 要素にアノテーションを付けられ、Swift ファイル保存時のホットリロードで実行中アプリの SwiftUI プレビューをリアルタイム更新できる。データはローカル完結でクラウドには送らない設計。2026 年 1 月にオープンソース化。

## 💡 今日のおすすめ実践 Tip

**「v2.1.214 の進捗ハートビートで XcodeBuildMCP のビルドが "フリーズ" か "実行中" かを即座に識別する」**

v2.1.214 以前は、XcodeBuildMCP の `BuildProject` が走っている間、Claude Code の TUI が静止したままになるため「ビルドが詰まったのか、それともただ時間がかかっているだけなのか」を区別する手段がありませんでした。今回の進捗ハートビートにより、長時間のツール呼び出し中に定期的な「⏱ still running（経過時間）」インジケーターが TUI に表示されるようになりました。

**実務での活用ポイント**

ハートビートが止まった時刻を記録しておき、XcodeBuildMCP のデフォルトタイムアウト（10 分）と比較することでフリーズ判定ができます。

```bash
# XcodeBuildMCP のビルドを時間計測しながら走らせる例（Claude Code 外で別ターミナルから）
time xcodebuild build \
  -scheme MyApp \
  -destination 'platform=iOS Simulator,name=iPhone 16 Pro' \
  -configuration Debug \
  2>&1 | grep -E "error:|warning:|Build succeeded|Build FAILED"
```

**v2.1.212 の MCP 自動バックグラウンド化との組み合わせ**

| バージョン | 機能 | XcodeBuildMCP での効果 |
|---|---|---|
| v2.1.212 | MCP 2 分超で自動バックグラウンド化 | ビルド中でも iPhone からの追加指示を受け付けられる |
| v2.1.214 | 進捗ハートビート | バックグラウンド化したビルドが「まだ生きているか」が TUI で確認できる |

**Issue #78878 への備え（RC ブリッジがサイレントダウンする問題）**

長時間ビルドと Remote Control を組み合わせている場合、RC ブリッジが予告なくドロップする可能性があります（Issue #78878 で報告中）。以下の CLAUDE.md フック設定で、ビルド完了 or 失敗を push 通知して iPhone で気づけるようにしておくと、RC が切れていても取りこぼしを防げます：

```json
// .claude/settings.json に追記
{
  "hooks": {
    "PostToolUse": [{
      "matcher": "mcp__XcodeBuildMCP__BuildProject",
      "hooks": [{
        "type": "command",
        "command": "curl -s 'https://api.day.app/<YOUR_BARK_KEY>/ビルド完了' > /dev/null"
      }]
    }]
  }
}
```

RC ブリッジの自動再接続が未実装の現状（Issue #78878）では、ビルド完了通知を別チャンネルで受け取る設計が最も安定した運用方法です。
