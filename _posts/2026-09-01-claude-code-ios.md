---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-09-01"
date: 2026-09-01 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- **Claude Sonnet 5 の $2/$10 価格が恒久化 — 9/1 からの値上げ中止**（[Anthropic 確認：Datafloq](https://datafloq.com/anthropic-confirms-claude-sonnet-5-prices-rise-50-on-september-1/)）— 6 月ローンチ時に「8/31 までの導入価格」と明示されていた $2/$10 /MTok が標準料金として恒久化。予定されていた $3/$15 への 50% 値上げは取り消された。XcodeBuildMCP や CI で Claude Code API を呼ぶ iOS チームのコスト計画に直接影響する朗報。

- **Claude Code 週間利用上限が 9/14 から変更 — ベース比 +25% 恒久化だが実効は -17%**（[daily.dev 解説](https://daily.dev/posts/claude-usage-limits-permanent-25-increase-starting-september-14-temporary-50-boost-ending-septem-axa5o5lgi)）— 9/14 以降、Pro・Max・Team・Enterprise の標準週間上限がベースから永続的に 25% 引き上げられる。ただし現在有効な一時的 +50% ブーストは 9/13 に終了するため、現ユーザーの実感としては現行比 17% 減になる（ベース 100 → 一時 150 → 新上限 125）。長時間の iOS ビルド解析セッションを多用しているチームは 9/14 以降の上限消費ペースを再確認しておくこと。

## 🛠 GitHub の動き

- [Issue #220 — Claude Code が MCP SDK 1.26.0 のフィールド（instructions / annotations / execution）により XcodeBuildMCP の全ツールをサイレントに消去する（getsentry/XcodeBuildMCP / OPEN）](https://github.com/getsentry/XcodeBuildMCP/issues/220) — XcodeBuildMCP v2.0.7 以降に含まれる MCP SDK 1.26.0 が initialize レスポンスに `instructions`（約 1500 文字のドキュメント文字列）と追加 `capabilities`（`listChanged` / `resources` / `logging`）を、ツールリストに `execution: { taskSupport: "forbidden" }` および `annotations` を付与するようになったが、Claude Code のクライアントがこれらの未知フィールドを正しく処理できずに**全ツールを無効扱い**にするバグ。サーバーは「Connected」と表示されるが、ツールリストがゼロになり XcodeBuildMCP の 71 本のツールがすべて消える。**暫定回避策**：Node.js プロキシスクリプトで MCP レスポンスから問題フィールドを strip してから Claude Code に転送する（詳細は下の今日の Tip 参照）。または Claude Code を最新バージョンにアップデートし、XcodeBuildMCP 側の修正版リリースを待つ。

- [Issue #284 — `buildAndTest` 実行時に `-resultBundlePath` を上書きできず code coverage が取得できない（getsentry/XcodeBuildMCP / CLOSED v2.3.0）](https://github.com/getsentry/XcodeBuildMCP/issues/284) — XcodeBuildMCP が内部で `-resultBundlePath` を自動設定するため、ユーザーが `--extra-args` で同オプションを渡すと「`option '-resultBundlePath' may only be provided once`」エラーになりテストが失敗する問題。v2.3.0 でクローズ済みのため `brew upgrade xcodebuildmcp` で最新版にアップデートすれば解消する。旧バージョンを使っている場合の代替策として、`xcodebuild` を直接 Bash で実行してからカバレッジレポートを手動処理する手順が issue に記録されている。iOS チームで XcodeBuildMCP 経由のコードカバレッジ取得に失敗しているケースはバージョン確認を推奨。

## 📝 日本語コミュニティ

- [Xcode MCP × Claude Code プラグインで、iOS ビルドを自動化する（Zenn / kyoichi）](https://zenn.dev/kyoichi/articles/claude-code-plugin-xcode-mcp-hybrid) — Xcode 26.3 のネイティブ MCP（mcpbridge）と XcodeBuildMCP を Claude Code プラグインから同時に扱うハイブリッド構成を解説した記事。2026-08-31 にも同著者の別記事（スキルシステム活用版）を紹介しているが本稿はプラグインの責務分担に焦点を当てた続編に当たる。

## 🌐 海外コミュニティ / Tips

- [Two MCP Servers Made Claude Code an iOS Build System（Blake Crosley）](https://blakecrosley.com/blog/xcode-mcp-claude-code) — XcodeBuildMCP（ビルド・テスト）と Xcode 26.3 ネイティブ MCP（プレビューレンダリング・スニペット実行）を並列で Claude Code に接続し、「コード生成 → ビルド → SwiftUI プレビューキャプチャ → UI テスト」を一連のエージェントループで完結させる構成を詳解した実践記事。どの操作をどちらの MCP に委ねるかの振り分けロジックが具体的で参考になる。

## 💡 今日のおすすめ実践 Tip

**「XcodeBuildMCP ツールが Claude Code から消えたときの Node.js プロキシ回避策」**

Issue #220 により、XcodeBuildMCP v2.0.7 以降で Claude Code からすべての MCP ツールが見えなくなる場合があります。公式修正が入るまでの間、以下の Node.js プロキシスクリプトで MCP レスポンスを正規化することで回避できます。

**プロキシスクリプト（`mcp-proxy.mjs`）**

```js
// XcodeBuildMCP の MCP SDK 1.26.0 非互換フィールドを strip して
// Claude Code に転送する最小プロキシ
import { createServer } from "net";
import { spawn } from "child_process";

const TARGET_CMD = "npx"; // or "xcodebuildmcp" if installed via brew
const TARGET_ARGS = ["-y", "xcodebuildmcp"];

const server = createServer((clientSock) => {
  const child = spawn(TARGET_CMD, TARGET_ARGS, { stdio: ["pipe", "pipe", "inherit"] });

  let buf = "";
  child.stdout.on("data", (chunk) => {
    buf += chunk.toString();
    const lines = buf.split("\n");
    buf = lines.pop(); // 未完了行を保持
    for (const line of lines) {
      if (!line.trim()) continue;
      try {
        const msg = JSON.parse(line);
        // initialize レスポンスから非互換フィールドを削除
        if (msg.result?.serverInfo) {
          delete msg.result.instructions;
          if (msg.result.capabilities) {
            delete msg.result.capabilities.listChanged;
            delete msg.result.capabilities.resources;
            delete msg.result.capabilities.logging;
          }
        }
        // ツール定義から非互換フィールドを削除
        if (Array.isArray(msg.result?.tools)) {
          msg.result.tools = msg.result.tools.map((t) => {
            const { execution, annotations, ...rest } = t;
            if (rest.inputSchema) delete rest.inputSchema.$schema;
            return rest;
          });
        }
        clientSock.write(JSON.stringify(msg) + "\n");
      } catch {
        clientSock.write(line + "\n");
      }
    }
  });

  clientSock.pipe(child.stdin);
  child.on("exit", () => clientSock.destroy());
  clientSock.on("close", () => child.kill());
});

server.listen(7878, "127.0.0.1", () =>
  console.error("MCP proxy listening on 127.0.0.1:7878")
);
```

**Claude Code の MCP 設定（`.claude/settings.json`）**

```json
{
  "mcpServers": {
    "xcodebuildmcp": {
      "command": "node",
      "args": ["path/to/mcp-proxy.mjs"],
      "type": "stdio"
    }
  }
}
```

プロキシを経由させると `instructions` や `execution: { taskSupport: "forbidden" }` などの Claude Code が未対応のフィールドが除去され、ツール 71 本が通常どおり表示されるようになります。根本修正は XcodeBuildMCP 側と Claude Code 側の両方でリリース待ちのため、`brew upgrade xcodebuildmcp` で最新版に更新しても解消しない場合はこの回避策を試してください。
