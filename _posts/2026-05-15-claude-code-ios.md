---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-05-15"
date: 2026-05-15 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Claude Code v2.1.141 (May 13)](https://code.claude.com/docs/en/changelog) — フック JSON 出力に **`terminalSequence` フィールド**を追加。制御端末なしでデスクトップ通知・ウィンドウタイトル変更・ベル音を hook から発行できるようになり、iOS のロングビルドを夜間バックグラウンドで流しながら完了通知を受け取る構成が容易に。`claude project purge [path]` コマンドでプロジェクトのトランスクリプト・タスク・ファイル履歴・設定エントリをまとめて削除可能に（`--dry-run` / `-i` / `--all` 対応）。企業環境向けに `CLAUDE_CODE_PLUGIN_PREFER_HTTPS` 環境変数も追加され、GitHub プラグインの clone を SSH の代わりに HTTPS で行えるように。思考が 10 秒以上続くと spinner がアンバー色に変わって長時間 thinking 中であることを視覚的に警告する改善も含む。

## 🛠 GitHub の動き

- [ndrblinov/xcode-mcp-manager](https://github.com/ndrblinov/xcode-mcp-manager) (v0.0.1, 公開 March 19, 2026) — Apple 純正 `xcrun mcpbridge` の接続をフルオート管理する Claude Code MCP サーバー。Xcode の起動を 2 秒ごとにポーリングして検出・mcpbridge プロセスを自動起動し、Xcode が終了→再起動しても Claude Code 側の再設定なしに再接続する。AppleScript で Xcode の「許可」ダイアログを自動クリックするため、無人エージェントワークフローでも人手操作ゼロ。ワークスペース管理・ビルド/テスト・コード補完・ナビゲーションなど 24 ツールを提供。`npm install -g xcode-mcp-manager` で導入後、Claude Code の MCP 設定に追加するだけ（Xcode 26.3+ / Node.js 22+ が必要）。
- [[BUG] swift-lsp plugin: LSP tools not exposed to Claude (#21335)](https://github.com/anthropics/claude-code/issues/21335) — 公式 `swift-lsp@claude-plugins-official` をインストールしても Claude が `find_references` / `go_to_definition` などの LSP ツールを認識できないバグが報告中。プラグインディレクトリに README.md しか存在せずツールの wiring がされていないのが原因の模様。回避策は現時点で未公式。Swift LSP の恩恵（リアルタイム型チェック・参照ジャンプ）を期待して導入した iOS 開発者は注意が必要。

## 📝 日本語コミュニティ

- [Claude Codeで5日でiOSアプリをApp Storeに出した全記録 — 何を任せて何を任せちゃダメか](https://zenn.dev/seeda_yuto/articles/claude-code-ios-app-5days) (seeda_yuto, Zenn) — SwiftUI + CryptoKit + MapKit を用いた iOS アプリを 5 日・55 コミット・33 ファイル・約 4,000 行で完成させた実録。「任せてよかったこと」と「任せてはいけないこと」の線引きを具体的に示しており、Claude Code × iOS 開発の適用範囲を判断する実務的指針として参考になる。
- [非エンジニアがClaude Codeだけで、iOSアプリをApp Storeに出した話](https://zenn.dev/naoki_dev/articles/098a689c4acdd5) (naoki_dev, Zenn) — iOS 開発未経験の UI/UX デザイナーが Claude Code を頼りにアプリを完成させ App Store リリースまで達成した記録。Swift の知識がない状態でどこまで Claude に委任できるか、どこで壁に当たったかが率直に書かれており、エンジニア以外がチームに加わる場合の想定として参考になる。

## 🌐 海外コミュニティ / Tips

- [Read It Now: another iOS app with Claude Code](https://medium.com/@franco.torriani/another-ios-app-with-claude-code-433ba9bd5f8b) (Franco Torriani, Medium, March 2026) — 2025 年 12 月に開発開始し 2026 年 3 月 21 日に App Store 申請、4 月 9 日にリリースした RSS リーダーアプリの開発記録。実質の開発稼働日は約 15 日。Claude Code が生成したコードの品質と「自分が何を理解していないまま進んでいたか」への反省が正直に記されており、Claude Code でリリースまで到達した現実的な工数感をつかめる。
- [The Complete Guide to Building an iOS App with Claude Code (No Xcode Required)](https://dev.to/marypiakovski/the-complete-guide-to-building-an-ios-app-with-claude-code-no-xcode-required-o5b) (DEV Community) — Expo（React Native）を使って Claude Code から Xcode なし・Swift なしで iOS アプリを構築する手順を解説。`expo start` で QR コードをスキャンするだけで実機確認ができるため、Swift 環境セットアップコストを省いたプロトタイプ・PoC 向けのアプローチとして紹介。Swift ネイティブ開発とは別の選択肢として把握しておくと用途に応じた使い分けができる。

## 💡 今日のおすすめ実践 Tip

**`terminalSequence` Hook で iOS ビルド完了をデスクトップ通知に変換する**

v2.1.141 で追加された `terminalSequence` フィールドを使うと、Claude Code の PostToolUse フックから制御端末がなくても OS ネイティブの通知を発行できます。夜間の無人ビルド・テストランが終わったら通知を受け取る設定例を示します。

```json
// .claude/settings.json
{
  "hooks": {
    "Stop": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "osascript -e 'display notification \"Claude セッション完了\" with title \"Claude Code\" sound name \"Glass\"'"
          }
        ]
      }
    ],
    "PostToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "grep -q 'BUILD SUCCEEDED' <<< \"$TOOL_OUTPUT\" && osascript -e 'display notification \"ビルド成功\" with title \"Xcode Build\" sound name \"Funk\"' || true"
          }
        ]
      }
    ]
  }
}
```

`terminalSequence` を直接使わなくても `osascript` 経由で同等の通知が出せますが、v2.1.141 以降はフック側で `terminalSequence` に iTerm2 の bell escape シーケンス（`\a`）を仕込むことでターミナルアプリ側の通知機能も合わせて呼び出せます。`/goal` コマンドや `--bg` セッションで長時間処理を流しているとき、Agent View で確認しに行かなくても「完了した」を即知れる構成はストレスを大幅に下げます。
