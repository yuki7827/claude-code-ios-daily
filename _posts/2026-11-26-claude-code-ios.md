---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-11-26"
date: 2026-11-26 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Claude Code v2.1.332（2026-11-24〜25）](https://github.com/715494637/claude-code-update/releases/tag/2.1.332) — **リスク・破壊的操作のデフォルト確認化**（データ損失や意図しないコマンド実行を防ぐため、危険なアクションは自動的に確認ダイアログを挟むように変更）、**MCP サーバーホスト型スキル（beta）**（`/skills` メニューに `server:スキル名` 形式で表示）、**新フック `PreSkillShellCommand`**（スキル内のインラインシェルコマンドを実行前にブロック可能）が主な追加。Code Mode 関連の修正も多数（ワークフロー一時停止・長いスクリプトでサブエージェントが止まる・セッション再開でコンテキスト消失）。全 150 件の CLI 変更を含む大型リリース。iOS 観点ではリスク操作の確認強化により誤ったビルド成果物の削除や `git reset --hard` の誤発動を防ぎやすくなった。

## 🛠 GitHub の動き

- 該当なし（本日時点で直近 5 投稿と重複しない iOS 関連の新規 Issue / PR は確認できず）

## 📝 日本語コミュニティ

- [Xcode MCP×Claude Codeプラグインで、iOSビルドを自動化する（Zenn / kyoichi）](https://zenn.dev/kyoichi/articles/claude-code-plugin-xcode-mcp-hybrid) — Apple 公式 `xcode` プラグインと `xcrun mcpbridge` を**同時に有効化するハイブリッド構成**の実践記録。プラグイン単体では Xcode を直接操作できるが `xcodebuild` コマンドが使えない場面があり、MCP bridge を併用することで「Xcode GUI 操作（プレビュー取得・設定変更）は mcpbridge 経由、ビルド・テスト自動化は `xcodebuild` CLI 経由」と役割分担できる。重複 MCP 登録による接続競合を避けるため、`MCP_XCODE_PID` で Xcode プロセスを明示的に指定する設定例も掲載。

- [iOS アプリのテスト結果を確認する Claude Code スキルを作ってみた（Qiita / ddpi）](https://qiita.com/ddpi/items/401ead85348a100df240) — `xcodebuild test` の出力を整形・要約して Claude に渡すカスタムスキルを自作した実践記事。生の `xcodebuild test` ログは数千行になることがあり、失敗テストの特定や原因推定に使えるトークンが大量に消費される問題を解消。スキルはテスト名・失敗行・エラーメッセージだけを抽出してプロンプトに注入する設計で、テスト失敗 1 件あたりの消費トークンを約 90% 削減できた事例を紹介している。

- [【第2弾】VS CodeのClaude Codeで「iOSコードレビュー → 修正 → Xcodeビルド確認 → テスト項目作成」を自動化する（Qiita / 4q_sano）](https://qiita.com/4q_sano/items/effe51de5e9654777f1e) — 前回（レビュー→修正→ビルド確認）に続いて**テスト項目自動生成**を追加したシリーズ第 2 弾（初心者向け）。VS Code 拡張経由の Claude Code でビルドが通った直後に `generate-test-spec` スキルを呼び出してテストケース一覧 Markdown を出力するフローを構築。Xcode の `.xctestplan` ファイルにインポートできる形式への変換スクリプトも掲載されており、コードレビューからテスト仕様書作成まで 1 セッションで完結できる。

## 🌐 海外コミュニティ / Tips

- [Two MCP Servers Made Claude Code an iOS Build System（blakecrosley.com）](https://blakecrosley.com/blog/xcode-mcp-claude-code) — XcodeBuildMCP（現 MobileBuildMCP）と Apple の `xcrun mcpbridge` を両方有効にして Claude Code を iOS ビルドシステムとして運用する構成を解説。MobileBuildMCP は Xcode が起動していなくても `xcodebuild` を直接たたけるのに対し、`mcpbridge` は起動中の Xcode インスタンスへの接続が前提。**ビルド・テスト自動化は MobileBuildMCP、SwiftUI プレビュー取得・ドキュメント検索・シミュレーター UI 操作は mcpbridge** という使い分けが最もトークン効率が良いと結論付けており、両者を競合させず並立させる `.claude/settings.json` の設定例が掲載されている。

- [Swift Coding Agent Guide: Building Native iOS Apps with AI in 2026（baeseokjae.github.io）](https://baeseokjae.github.io/posts/swift-coding-agents-native-ios-development-ai/) — Claude Code（Claude Agent 経由）・Codex CLI・Cursor Agent の 3 ツールを実際の iOS プロダクト開発で比較したガイド（最終更新 2026 年秋）。**SwiftUI の全体設計・TCA アーキテクチャの整合性確認・複雑なリファクタリングでは Claude Code がリードし、ビルド→修正→再ビルドの高速サイクルでは Codex CLI が速い**という評価。iOS 開発特有の注意点として「Swift マクロのエラーメッセージは展開前のコードを使って説明しないと Claude が誤った箇所を修正しやすい」「SwiftData マイグレーションはスキーマバージョン全体を必ずコンテキストに入れる」の 2 点が実務的なアドバイスとして挙げられている。

## 💡 今日のおすすめ実践 Tip

**v2.1.332 新フック `PreSkillShellCommand` で スキル内コマンドを iOS プロジェクト向けに安全化する**

v2.1.332 で追加された `PreSkillShellCommand` フックは、スキル（Markdown インストラクション内で `<bash>` タグなどで定義されるインラインシェルコマンド）が実行される直前に割り込める。MobileBuildMCP や ios-simulator-skill など外部スキルが走らせるコマンドをプロジェクト固有のルールでフィルタリングしたいときに使える。

`.claude/settings.json` の設定例（`xcodebuild clean` の silent 実行を明示的に許可しつつ、`rm -rf` 系だけブロック）:

```jsonc
{
  "hooks": {
    "PreSkillShellCommand": [
      {
        "matcher": ".*",
        "hooks": [
          {
            "type": "command",
            "command": "bash -c 'cmd=\"$CLAUDE_SKILL_SHELL_COMMAND\"; if echo \"$cmd\" | grep -qE \"rm -rf|git reset --hard|git clean -fd\"; then exit 1; fi'",
            "on_failure": "block"
          }
        ]
      }
    ]
  }
}
```

11-23 に紹介した `PreToolUse` フックが Edit/Write ツール経由の `.pbxproj` 書き換えを防ぐのに対し、`PreSkillShellCommand` はスキルが走らせるシェルコマンドレベルでの安全弁となる。両方を組み合わせることで、外部スキルとネイティブツール操作の両面からプロジェクトファイルを保護できる。
