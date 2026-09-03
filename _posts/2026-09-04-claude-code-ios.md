---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-09-04"
date: 2026-09-04 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- **v2.1.259（2026-09-02）リリース**（[Claude Code 変更ログ](https://code.claude.com/docs/en/changelog)）— iOS 開発・CI 環境に直接関係する変更が複数。① **`managedMcpServers` 設定でエンタープライズ組織が HTTP/SSE MCP サーバーを全ユーザーに一括配布可能に** — `.mcp.json` と同形式で定義し、管理者が MDM（macOS は Jamf 等、`/Library/Application Support/ClaudeCode/managed-mcp.json`）経由で展開することで、iOS 開発チーム全員に XcodeBuildMCP を自動接続させられる（下の今日の Tip 参照）; ② **`--permission-prompts none` フラグを追加** — 非対話型ヘッドレスホスト向けに、権限確認が必要な操作をすべて自動拒否するモード。GitHub Actions 等の iOS CI 環境でスケジュール実行する際に余計なプロンプトで止まらなくなる; ③ **`timeFormat`/`timeZone` 設定を追加** — 12/24 時間表示・UTC・strftime パターンから選択可能に; ④ **バグ修正: Stop コマンドがリモートコントロールセッションのバックグラウンドエージェントを実際に停止しない問題を修正** — iPhone Remote Control でビルドジョブを管理していた際に Stop が効かずエージェントが走り続けるケースが解消; ⑤ **バグ修正: 並行セッションが `~/.claude.json` の変更を相互に上書きする問題を修正** — 複数端末や複数ターミナルで Claude Code を同時起動していると設定が競合・消失することがあった問題が解消; ⑥ **バグ修正: `CLAUDE_CODE_MAX_CONTEXT_TOKENS` が Vertex 形式モデル ID で無視される問題を修正** — Vertex AI 経由で Claude を使う iOS CI 環境でコンテキスト制限が効かなかった問題; ⑦ **バグ修正: worktree 分離がフック作成の worktree を拒否する問題を修正** — iOS プロジェクトで worktree を使った並列ブランチ作業中にフック由来の worktree が排除されていた問題; ⑧ **VSCode 改善** — セッションリストに Active クイックフィルタ追加・ACCOUNT & USAGE / SESSION MANAGER セクションの折りたたみ対応

## 🛠 GitHub の動き

- **[エンタープライズ MCP アクセス制御の公式ドキュメント整備（code.claude.com/docs/en/managed-mcp）](https://code.claude.com/docs/en/managed-mcp)** — v2.1.259 の `managedMcpServers` 追加に合わせて、組織が MCP サーバーへのアクセスを制御するための公式ガイドが整備された。パターン別に「固定サーバーセットの配布（`managed-mcp.json`）」「許可リスト/拒否リストによるポリシー制御」「MCP の完全無効化」が解説されている。iOS チームで XcodeBuildMCP を全開発機に自動展開したい場合や、逆に未承認 MCP サーバーの接続を禁止したい場合の設定方法が網羅されている。OpenTelemetry を使った「組織内でどの MCP ツールが実際に使われているか」の監視方法も記載あり。

## 📝 日本語コミュニティ

- [SwiftData × Claude Code で永続化層を設計する — @Model 設計からマイグレーションまで実務で詰まらないための完全ガイド（Qiita / kotaro_ai_lab）](https://qiita.com/kotaro_ai_lab/items/119901d60f34e07da801) — `@Model` マクロの設計指針から `VersionedSchema` を使ったマイグレーション計画まで、SwiftData の永続化層を Claude Code に任せる際のプロンプト設計と注意点をまとめた記事。「Claude はスキーマ変更の影響範囲をコードから読み取るのが得意だが、マイグレーションステップの順序指定は人間が明示的に指示する必要がある」という実務上の知見が参考になる。

- [コードを 1 行も書かずに 25,000 行の iOS アプリを作った話 ― Claude Code で完全 Vibe Coding（Qiita / john-rocky）](https://qiita.com/john-rocky/items/b5a24858e797c954b16f) — 155 ファイル・25,000 行超の iOS アプリを 11 日間で Claude Code のみで構築した体験記。「自分でコードを書かない」という制約のもとで XcodeBuildMCP を組み合わせ、ビルドエラーの自己修正ループを回し続けた記録。Swift の知識があっても「Claude に説明する能力」が開発速度を左右すると結論付けており、CLAUDE.md の書き方・プロンプト設計の重要性が具体例つきで語られている。

- [Claude Code 標準 Skill を iOS アプリ開発でどう使うか：公式ドキュメントを根拠に整理する（Qiita / 4q_sano）](https://qiita.com/4q_sano/items/0ecf0fc7353d751f601c) — `/code-review`・`/security-review`・`/run` などの組み込みスキルを iOS プロジェクトで活用する際の使い分けを、公式ドキュメントを根拠に整理した記事（同著者の「第 2 弾」自動化記事とは別稿）。「どのスキルがビルド検証に使えるか」「`/run` で iOS シミュレーターが起動するか」など、実際に試した結果が記載されている。

## 🌐 海外コミュニティ / Tips

- [10 Tips to Maximize Claude Code for iOS Development（Medium / Jerry PM, CodeToDeploy）](https://medium.com/codetodeploy/10-tips-to-maximize-claude-code-for-ios-development-29027edc51a7) — iOS 開発で Claude Code を最大限活用するための実践的な 10 のヒントをまとめた記事。「CLAUDE.md にプロジェクト構造と使用フレームワーク（TCA / SwiftData 等）を明記する」「`xcodegen` の `project.yml` と組み合わせて Xcode プロジェクトファイルの生成まで自動化する」「テストはファイル単位でなくモジュール単位で指示する」などが紹介されている。Claude Code は Swift ロジック層・テスト層で強く、UI の最終確認は Xcode で人間が行うハイブリッド運用が推奨されている。

## 💡 今日のおすすめ実践 Tip

**「`managed-mcp.json` で iOS 開発チーム全員に XcodeBuildMCP を自動展開する」**

v2.1.259 で加わった `managedMcpServers` と公式 Managed MCP ガイドを組み合わせると、チーム全員の開発機に XcodeBuildMCP を一括配布し、メンバーが個別に `claude mcp add` を実行する手間をゼロにできます。macOS の場合は Jamf などの MDM で以下のファイルを配布します。

**`/Library/Application Support/ClaudeCode/managed-mcp.json`**

```json
{
  "mcpServers": {
    "xcodebuildmcp": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "xcodebuildmcp@latest", "mcp"]
    }
  }
}
```

このファイルが存在する端末では：

- `claude mcp list` に `xcodebuildmcp` が自動で表示される
- メンバーは追加の MCP サーバーを `claude mcp add` で自由に追加できる（`managed-mcp.json` の存在だけでは追加を禁止しない。禁止するには `allowedMcpServers` 設定が必要）
- `claude mcp add --transport http test https://example.com` を試みると `enterprise MCP configuration is active...` と表示され、管理外のサーバーが混入するリスクを把握できる

**CI 環境（セルフホスト GitHub Actions ランナー）での活用**

```yaml
# .github/workflows/claude-ios-ci.yml
jobs:
  ios-review:
    runs-on: [self-hosted, macos]
    steps:
      - uses: actions/checkout@v4
      - name: Run Claude Code (headless, no prompts)
        run: |
          claude --permission-prompts none \
            "XcodeBuildMCP で MyApp をビルドしてテストを実行し、失敗があれば原因を報告してください"
        env:
          ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
```

セルフホストランナーのホスト OS に `managed-mcp.json` を配置しておけば、CI 実行ごとに XcodeBuildMCP が自動接続されます。`--permission-prompts none` を付けることで、権限確認ダイアログで CI が止まる問題も解消します。
