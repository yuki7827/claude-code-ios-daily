---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-06-16"
date: 2026-06-16 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

該当なし

## 🛠 GitHub の動き

- [gabelul/screenslop](https://github.com/gabelul/screenslop)（v0.1.5・2026-06-15） — Apple系アプリ向けの「証拠ベース」UIレビューCLI。`screenslop see` でシミュレータのスクリーンショット・アクセシビリティツリー・ログを取得し、`critique` で問題点を指摘、`fix` でSwiftUIへのnarrowな修正を適用、`verify` で再キャプチャして改善を確認する一連のループをClaude Code向けのエージェントスキルとして提供（`npx skills add gabelul/screenslop --skill screenslop`）。Baguette → XcodeBuildMCP → `xcodebuild`+`simctl` の順でランタイム証拠を優先する設計。

- [jlreyes/swift-agent-kit](https://github.com/jlreyes/swift-agent-kit)（新規・2026-06-11作成） — Swift/SwiftUIエージェント向けの開発キット。LLMが書きがちな古いパターン（ObservableObject・DispatchQueue・Swift 6並行性違反など）の回避ルール、実SDKのシグネチャ／DocC／HIG／WWDCトランスクリプトを検索してハルシネーションを防ぐApple文書検索、ローカルのXcode 27から公式7スキルを抽出する仕組み、`.trace`ファイルをDuckDBに変換してSQLで解析するInstruments連携などを収録。プラグインマーケットプレイス経由でSessionStartフック付きでClaude Codeに導入できる。

- [TaylorFinklea/hermes-voice](https://github.com/TaylorFinklea/hermes-voice)（新規・2026-06-06作成） — Claude Code / Codex / OpenCodeなどのローカルコーディングエージェントを音声操作するiOS + watchOS製フロントエンド。Push-to-talkでmacOS上のFastAPIバックエンドに音声を送り、OpenAI/Groq/ElevenLabs/ローカルPiper・Whisperなど複数のSTT/TTSプロバイダに対応。Tailscale経由で外出先からMac上のClaude Codeセッションに声で指示を出すような使い方を想定している。

## 📝 日本語コミュニティ

該当なし

## 🌐 海外コミュニティ / Tips

- [Xcode 27 Agentic Coding: MCP, On-Device AI, Agent Skills Explained（byteiota）](https://byteiota.com/xcode-27-agentic-coding-mcp-guide/) — Xcode 27に同梱された `mcpbridge` バイナリが、MCPをXPC経由でXcodeのライブプロセスに橋渡しし、IDE全体を「汎用MCPホスト」化する仕組みを解説。Claude Code・Codex・CursorなどMCP対応エージェントは `mcpbridge` 経由でアクティブな診断情報・シンボル解析・SwiftUIプレビュー・Swift REPLに直接アクセスできる一方、Xcodeプロセスが起動していないとMCP呼び出しが失敗するという制約も明記されている。

## 💡 今日のおすすめ実践 Tip

**「`mcpbridge` の制約を前提に、swift-agent-kit（実装規約）→ screenslop（実機UI検証）の2段ループを組む」**

今日紹介した2つのGitHubプロジェクトは、Xcode 27の `mcpbridge` が開いた「Claudeから Xcode のライブ状態に直接アクセスできる」という土台の上で、「実装規約の遵守」と「実機での見た目検証」をそれぞれ担う構成になっています。

```bash
# 1. Swift 6/SwiftUI規約 + Xcode 27公式スキル抽出 + Instruments解析を導入
/plugin marketplace add jlreyes/swift-agent-kit

# 2. シミュレータでの証拠ベースUIレビューを導入
npx skills add gabelul/screenslop --skill screenslop
```

Claude Code への指示例:

```
1. swift-agent-kit のAppleドキュメント検索とXcode 27公式スキルを使って、
   新しい設定画面をSwift 6 / SwiftUI の最新作法で実装して。
   ハルシネーションしたAPIがないか falsification チェックも通して。

2. 実装が終わったら screenslop see → critique で
   シミュレータのスクリーンショットとアクセシビリティツリーから
   見た目の問題を洗い出し、fix で narrow な修正を適用、
   verify で再キャプチャして改善を確認して。

3. mcpbridge 経由のXcode MCPツールは Xcode が起動していないと
   失敗するので、CIやヘッドレス実行では XcodeBuildMCP 側の
   ビルド/テストパスにフォールバックするよう設計しておいて。
```

`swift-agent-kit` の「Xcode-as-MCP-worker」ガイドと `screenslop` の「Baguette → XcodeBuildMCP → xcodebuild+simctl」という優先順位は、どちらも `mcpbridge` が Xcode 起動中のみ機能するという制約を前提にしています。Xcodeを閉じたCI実行とXcode起動中のインタラクティブ開発を切り替える際は、この優先順位に沿ってツールを選び分けると、エージェントが「ツールが使えない」エラーで止まりにくくなります。
