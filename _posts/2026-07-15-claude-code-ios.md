---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-07-15"
date: 2026-07-15 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Claude Code v2.1.208（2026-07-14）](https://code.claude.com/docs/en/changelog) — **スクリーンリーダーモード**（`--ax-screen-reader` フラグまたは `CLAUDE_AX_SCREEN_READER=1`）を追加。`vimInsertModeRemaps` 設定で `jj` → Esc などのカスタムキーバインドが可能に。コーポレートランチャー対応の `CLAUDE_CODE_PROCESS_WRAPPER` 環境変数とフルスクリーンでのマウスクリック複数選択も追加。iOS 開発者に直接関係する変更は少ないが、Vim キーバインドカスタマイズは Terminal でのコード編集効率に効く。

- [Claude Code v2.1.209（2026-07-14）](https://code.claude.com/docs/en/changelog) — `/model` やその他ダイアログが `claude agents` バックグラウンドセッションでブロックされていたバグを修正（過剰なガードのリバート）。XcodeBuildMCP + `claude agents` でビルドループをバックグラウンド並列実行している場合にモデル切り替えが詰まる症状の解消。

## 🛠 GitHub の動き

- [Issue #68831 — Remote Control: iOS から添付した写真がデスクトップセッションでサイレントに消える（CLOSED 2026-07-14）](https://github.com/anthropics/claude-code/issues/68831) — iPhone の Claude アプリから Remote Control セッションへ写真を添付しても、デスクトップ側で空のメッセージとして届き画像ブロックが欠落していたバグが修正。同時に [Issue #74409（Remote Control: モバイルからのスクリーンショット添付が頻繁に同期失敗）](https://github.com/anthropics/claude-code/issues/74409) もクローズ。iPhone で Simulator のスクリーンショットを撮って Claude に直接貼り付けてレビューを依頼するワークフローが安定して使えるようになった。

- [Issue #30447 — Feature Request: `claude remote-control --headless` — TTY 依存なしでデーモン化可能なリモートコントロール（open・👍 34 件）](https://github.com/anthropics/claude-code/issues/30447) — 現在の `claude remote-control` は TTY（端末）依存のため systemd や launchd でデーモン化できず、Mac 再起動のたびに手動で再起動が必要。`--headless` フラグで TTY なしのバックグラウンド常駐を求める要望で、2026-07-14 に追加コメントあり。常時起動の iOS ビルドサーバーとして Mac を使う場合に重要な機能。

- [ZohaibAhmed/clauder — Go 製 HTTP API サーバーで iPhone から Claude Code を安全にリモート操作](https://github.com/ZohaibAhmed/clauder) — Go バックエンド + Next.js フロントエンド構成のオープンソースプロジェクト。公式 Remote Control が claude.ai 経由のクラウドリレーであるのに対し、Clauder は自前のコーディネーターサーバーを経由する設計で、Claude Code・Goose・Aider・Codex を同一インターフェースから操作可能。256-bit ランダムトークン（24 時間有効）＋ HTTPS トンネルで認証。iOS Xcode プロジェクトのルートで直接起動しても git リポジトリアクセスエラーが発生しない（Issue #44805 の回避策としても機能）。

## 📝 日本語コミュニティ

- [Claude CodeからXcodeが提供するMCPに接続する方法（Zenn / pepabo）](https://zenn.dev/pepabo/articles/4c0d1019ac1f7d) — Apple 公式の `xcrun mcpbridge` を使って Claude Code から Xcode MCP に接続する手順を詳説。`claude mcp add --transport stdio xcode -- xcrun mcpbridge` の 1 コマンドで接続でき、macOS 26 Tahoe + Apple Silicon が必要。API キーやクラウドリレーは不要でローカル完結する点が特徴。Xcode MCP の 20 ツール（ビルド・テスト・SwiftUI Preview・ドキュメント検索）が Claude から直接呼び出せるようになる。

- [mobile-mcp を使ってClaude CodeにiOSを操作させる方法（Zenn / jboydev）](https://zenn.dev/jboydev/articles/b7bfa15e3b2eeb) — `@mobilenext/mobile-mcp` を `claude mcp add mobile-mcp -- npx -y @mobilenext/mobile-mcp@latest` で追加すると、iOS シミュレータ（および実機）をブラウザ操作するように Claude Code に指示できる。Safari でページを開く・テキスト入力・スワイプなどを日本語でそのまま指示可能。XcodeBuildMCP がビルド・テストに強い一方、mobile-mcp は「実際のユーザー操作」シミュレーションに特化しており、ユーザージャーニーの E2E 検証を自然言語で記述できる。

- [Claude Code のリモートコントロールとスマホ通知の始め方（Zenn / schroneko）](https://zenn.dev/schroneko/articles/claude-code-remote-control-and-mobile-notification) — Remote Control で Claude Code を iPhone から操作しつつ、Notification Hook ＋ iOS アプリ「Bark」で許可待ち・タスク完了などのイベントをプッシュ通知する設定を解説。`~/.claude/settings.json` の `hooks` に `permission_prompt` / `idle_prompt` トリガーを設定し、バナー通知をタップすると Claude iOS アプリのセッションへ直接ジャンプする構成。ビルド待ちの間 iPhone を置いて別作業できるようになる実用的な設定記事。

## 🌐 海外コミュニティ / Tips

- [Letting Claude Code Drive the Simulator: Automated UI Verification via Screenshots（Zenn / shimo4228・英語記事）](https://zenn.dev/shimo4228/articles/xcodebuildmcp-ios-verification) — XcodeBuildMCP の `TakeScreenshot` ツールを使い、Claude Code が iOS Simulator をタップ操作しながらスクリーンショットで UI の正しさを自律検証する実装例。著者は暗記アプリ「Baki 検定」に「詰め込みモード」機能を実装し、コードを 1 行も書かずに Claude Code が実装→ビルド→シミュレータ起動→UI 操作→スクリーンショット確認→バグ修正のループを完走させた体験を報告。日英混在の Zenn 記事だが英語セクションも充実しており海外向けにも参考になる。

- [iOSアプリ開発に役立つAgentic Coding集（Zenn / mtfum）](https://zenn.dev/mtfum/articles/ios_agentic_coding) — Xcode MCP・XcodeBuildMCP・mobile-mcp の 3 つの MCP サーバーを「何に使うか」でマトリクス整理した記事。Apple MCP は IDE 統合（SwiftUI Preview・ドキュメント検索）に強く、XcodeBuildMCP はヘッドレスビルド・デバッグに強く、mobile-mcp は実際のユーザー操作シミュレーションに特化という使い分けを 2026 年 6 月時点の情報でまとめている。MCPを 複数組み合わせるときの選択基準として参考になる。

## 💡 今日のおすすめ実践 Tip

**「mobile-mcp + XcodeBuildMCP で Claude Code に E2E UI 検証を自律実行させる」**

今日紹介した `zenn.dev/jboydev` と `zenn.dev/shimo4228` の記事を組み合わせると、Claude Code が「ビルド → シミュレータ起動 → UI 操作 → スクリーンショット検証」まで無人で実行できます。

**1. 2 つの MCP を登録**

```bash
# Xcode ネイティブ MCP（SwiftUI Preview・ドキュメント検索）
claude mcp add --transport stdio xcode -- xcrun mcpbridge

# mobile-mcp（iOS シミュレータの UI 操作）
claude mcp add mobile-mcp -- npx -y @mobilenext/mobile-mcp@latest
```

**2. Claude Code への指示例（日本語でOK）**

```
iPhone 15 シミュレータで MyApp をビルドして起動し、
以下のユーザージャーニーを実行して各ステップでスクリーンショットを撮り、
UI に問題がないか確認してください：

1. オンボーディング画面が表示されることを確認
2. 「始める」ボタンをタップしてホーム画面に遷移
3. 上部の検索バーをタップして「Swift」と入力
4. 検索結果が表示されることを確認
5. 最初の結果をタップして詳細画面へ遷移

問題があれば修正してから再度同じジャーニーを実行してください。
```

**3. Bark 通知と組み合わせると外出中も安心**

`schroneko` 記事の Bark 設定を追加しておくと、E2E テストが完了（または Claude が人間の許可を求める）タイミングで iPhone にプッシュ通知が届きます。ビルドとテストを夜間バッチで走らせながら別作業できます。

**ポイント**: XcodeBuildMCP は `xcodebuild` を JSON 化してビルド精度を高め、mobile-mcp は実際のタップ・入力操作で現実のユーザー操作に近い検証を担います。2026-07-14 に iOS 写真添付バグ（Issue #68831 / #74409）が修正されたことで、「シミュレータのスクリーンショットを iPhone から Claude に送って確認させる」フローも安定して使えるようになりました。
