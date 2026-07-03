---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-07-04"
date: 2026-07-04 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Claude Code v2.1.200（2026-07-03）— デフォルト権限モードが "Manual" に統一・AskUserQuestion 自動継続廃止](https://code.claude.com/docs/en/changelog) — iOS チームに影響する安全寄りの破壊的変更。① **デフォルト権限モードが "Manual" に**（CLI・VS Code・JetBrains 全体）— これまで「default」だったモードが「manual」に改名・統一され、`--permission-mode manual` と `"defaultMode": "manual"` の両記法が受け付けられる。CI/CD で `bypassPermissions` や `dontAsk` を明示していなかったパイプラインは動作を再確認する必要がある。② **AskUserQuestion ダイアログが自動続行しなくなった** — 以前はアイドルタイムアウトで自動続行していたが廃止。`/config` でオプトイン設定が可能。③ **バックグラウンドセッションの安定性向上** — スリープ/スリープ解除後にセッションが中断する問題・クラッシュ後の再起動失敗を修正。`xcodebuild archive` など数時間かかる処理を夜間エージェントに任せる際の信頼性が向上。④ **MCP 設定クラッシュ修正** — `.claude.json` の `disabledMcpServers` / `enabledMcpServers` が配列でなかった場合のスタートアップクラッシュを修正。⑤ **tmux 3.4+ での描画ちらつき改善・スクリーンリーダー対応強化**。

- [Claude Code v2.1.199（2026-07-02）— 複数スラッシュスキル積み重ね・SSL 即時フェイル・CLAUDE_CODE_RETRY_WATCHDOG](https://code.claude.com/docs/en/changelog) — iOS ワークフローに効く 3 点。① **スラッシュスキルの積み重ね対応（最大 5 個）** — `/skill-a /skill-b do XYZ` のように複数スキルを先頭に置くと全スキルが同時ロードされる（従来は最初の 1 個のみ）。XcodeBuildMCP スキル + カスタム fastlane スキルを同時適用するユースケースで有効。② **ストリーミング部分出力の保持** — API が途中でエラーを返した場合、従来は応答が全破棄されていたが、受信済みの部分出力を保持したうえで不完全応答を通知するように変更。長い SwiftUI コード生成中のエラーで再送コストが下がる。③ **CLAUDE_CODE_RETRY_WATCHDOG** 環境変数で非容量関連の一時的エラーに対するリトライ上限を調整可能になった。また Linux で 50 秒ごとにデーモンが再起動する暴走バグ・macOS で SSH 起動が失敗するバグも修正された。

## 🛠 GitHub の動き

- [Zenn / kyoichi「XcodeBuildMCP × Claude Code スキルシステムで iOS ビルドを自動化する」](https://zenn.dev/kyoichi/articles/claude-code-xcodebuildmcp-ios-build) — 79 以上のツールを 16 のワークフローグループに整理した Claude Code スキルシステムと XcodeBuildMCP の組み合わせで iOS ビルドを自動化する手順を解説。スキル単位でツールを Claude にロードさせ、ビルド・テスト・シミュレータ操作を役割別に分離することでコンテキスト汚染を防ぎつつ自律ループを回す構成を紹介。

- [Zenn / pepabo「Claude Code から Xcode が提供する MCP に接続する方法」](https://zenn.dev/pepabo/articles/4c0d1019ac1f7d) — Xcode 26.3 の Apple 純正 MCP サーバー（`xcodemlsp`）へ Claude Code CLI から接続し、外部の XcodeBuildMCP と併用する設定手順を解説。純正 MCP は SwiftUI プレビューレンダリング・REPL・ドキュメント検索を担い、XcodeBuildMCP はビルド・テスト・シミュレータ操作を担う役割分担の実装例として参考になる。

## 📝 日本語コミュニティ

- [Xcode 26.3エージェント型コーディング入門 — Claude・Codex・MCPで開発を自動化（Qiita / kai_kou）](https://qiita.com/kai_kou/items/1b24ad62dde4c02ae4f0) — Xcode 26.3 の Agentic Coding 機能を網羅した入門記事。Claude Agent・OpenAI Codex・Apple 純正 MCP の役割を整理し、iOS プロジェクトへの初期導入から実際のコーディングフローまでをステップバイステップで解説。複数エージェントの切り替え方法と MCP サーバーの接続設定が具体的に示されており、Xcode 26.3 を使い始めたばかりの iOS 開発者の最初の一歩として有用。

- [Xcode 26.3のMCPとは何か — 7つのSkillsで実践する（Qiita / dsgarage）](https://qiita.com/dsgarage/items/7c4b13de4dd4b2ec2370) — 「なぜ Xcode に MCP が必要か」から始めて Apple 純正 MCP の 7 つの Skills（ビルド・テスト・プレビュー・ドキュメント検索等）を実際に動かしながら紹介した実践記事。Skills のインストールパス（`~/Library/Developer/Xcode/CodingAssistant/ClaudeAgentConfig/skills`）も明記されており、カスタム Skill を追加したい場合の参考になる。

- [Claude Code にシミュレータを渡したら自分でタップしてスクショで検証し始めた（Qiita / shimo4228）](https://qiita.com/shimo4228/items/3c6106d5407deea0cbf4) — XcodeBuildMCP の UI オートメーションツールを使って Claude Code がシミュレータ上で自律的にタップ操作・スクリーンショット取得・視覚的な検証を行う様子をキャプチャ付きで報告した記事。`tap_on_simulator`・`take_screenshot_simulator` ツールの組み合わせで「コードを書く → ビルド → タップして見た目を確認 → 修正」のループを人間が関与せず回せることを実証。

## 🌐 海外コミュニティ / Tips

- [Xcode 26.3 + Claude Agent — Model Swapping, MCP, Skills, Adaptive Configuration（Needone App / Medium）](https://needoneapp.medium.com/xcode-26-3-claude-agent-model-swapping-mcp-skills-and-adaptive-configuration-f8d54f93535d) — Xcode 26.3 RC に同梱された Claude Client は v2.1.14（Opus 4.5 ベース）だが、`~/Library/Developer/Xcode/CodingAssistant/Agents/Versions/26.3/` の `claude` バイナリを最新リリース（Opus 4.6 / v2.1.32）に差し替える **Model Swapping** テクニックを紹介。また MCP の設定ファイルパス（`~/.../ClaudeAgentConfig/.claude`）と Skills のインストール先を示し、Xcode の外部グローバル MCP 設定を Xcode Agent にも適用する方法（デフォルトでは遮断されている）を解説。Xcode で Claude Agent の能力を最大限引き出すためのカスタマイズリファレンスとして有用。

- [Xcode 26.3 + Claude Agent: What Actually Works (And What Doesn't)（marc0.dev）](https://www.marc0.dev/en/blog/ai-agents/xcode-26-3-claude-agent-what-actually-works-and-what-doesnt-1770494531265) — Xcode 26.3 の Claude Agent を実際に使い込んだ上で「動いたこと / 動かなかったこと」を率直にレポートした記事。うまく機能するのはシンプルな SwiftUI コンポーネント生成・小規模リファクタリング・エラーログ診断で、大規模リアーキテクチャや複数ファイル横断のコンテキスト保持には限界がある点を指摘。「Xcode 内のエージェントは補完ツール、大仕事は CLI の Claude Code に任せる」という分担指針が実用的。

## 💡 今日のおすすめ実践 Tip

**「v2.1.200 の権限モード変更を受けて iOS CI/CD の permission 設定を見直す」**

v2.1.200 からデフォルト権限モードが "Manual" に変わりました。これまでの "default" モードのまま CI を動かしていた場合、エージェントが確認プロンプトで止まるようになる可能性があります。iOS CI/CD の用途別に適切なモードへ明示的に切り替えましょう。

**用途別の推奨設定**

| 用途 | モード | 設定方法 |
|------|--------|----------|
| ローカル開発（通常） | `manual` | デフォルトのまま（プロンプトで確認） |
| CI ビルド・テスト（読み取り中心） | `dontAsk` | `--permission-mode dontAsk` |
| フルオート CI（書き込みあり・サンドボックス内） | `bypassPermissions` | `--permission-mode bypassPermissions` |

**dontAsk モードを iOS CI で使う場合の設定例**

```json
// .claude/settings.json（プロジェクトに commit）
{
  "defaultMode": "dontAsk",
  "permissions": {
    "allow": [
      "Bash(xcodebuild:*)",
      "Bash(swift build:*)",
      "Bash(swift test:*)",
      "Bash(fastlane:*)",
      "Bash(xcrun simctl:*)"
    ],
    "deny": [
      "Bash(git push:*)",
      "Bash(rm -rf:*)"
    ]
  }
}
```

`dontAsk` は allow ルールに一致するコマンドのみ実行し、一致しないものは自動拒否します。`bypassPermissions`（全許可）とは異なり **破壊的操作を防ぐ制御が残る**ため、ブランチ保護と組み合わせた iOS CI 運用に適しています。`bypassPermissions` はサンドボックス化された Docker コンテナ内など、完全に隔離された環境でのみ使用してください。
