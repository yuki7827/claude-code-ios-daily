---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-10-21"
date: 2026-10-21 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- 該当なし（2026-10-21 時点で直近の v2.1.303 は 10-20 キャッチアップ済み。v2.1.304 以降のリリースは未確認）

## 🛠 GitHub の動き

- **[#103525 \[BUG\] iOS Simulator パネルが静止画面でも常に 60fps で全解像度ストリーミングを継続（2026-10-19 open）](https://github.com/anthropics/claude-code/issues/103525)** — Claude Code Desktop の iOS Simulator パネルが、表示中のアプリが静止状態であっても・ディスプレイがオフであっても 60fps でフルフレームを送り続けるというバグが報告された。Mac の CPU / GPU・バッテリーを継続的に消費するため、長時間の開発セッションで意図せず電力を浪費する。10-20 時点で open かつ複数人が再現確認済み。

- **[#103378 Desktop: アプリが横向きに切り替えた後、iOS Simulator パネルのタップ座標がずれる（2026-10-19 open）](https://github.com/anthropics/claude-code/issues/103378)** — アプリ側から `UIInterfaceOrientationMask` で横向きを強制した場合に、Claude Code Desktop の iOS Simulator パネルが縦向きのまま残り、タップのヒットテスト座標がズレてしまう問題。UI テスト自動化や Claude による操作確認で意図しない座標にタップが飛ぶ。10-19 時点で open。

## 📝 日本語コミュニティ

- **[Xcode の Claude Agent・Codex が settings.json・config.toml でセットアップできるように（zenn.dev/treastrain）](https://zenn.dev/treastrain/articles/bc9435a36fe498)** — Xcode 26.5 で追加された「Enterprise Configuration」を解説。Claude Agent は `settings.json`、Codex は `config.toml` でアカウント設定を管理でき、接続先を Anthropic のクラウドだけでなく Amazon Bedrock・Google Vertex AI・Microsoft Foundry などのプロキシエンドポイントに切り替えられるようになった。プロジェクト単位でエージェントのモデルや API エンドポイントを固定できるため、社内セキュリティ要件がある企業での利用に道が開けた。

- **[XcodeBuildMCP × Claude Code スキルシステムで、iOS ビルドを自動化する（zenn.dev/kyoichi）](https://zenn.dev/kyoichi/articles/claude-code-xcodebuildmcp-ios-build)** — XcodeBuildMCP の公式スキル（`xcodebuildmcp` スキルと `xcodebuildmcp-cli` スキル）を Claude Code のスキルシステムと組み合わせ、ビルド・テスト・シミュレーター操作を Claude に委ねるワークフローを解説。raw の `xcodebuild` コマンドを直接叩くのではなく XcodeBuildMCP 経由にすることで、ツール呼び出しの再現性と可読性が上がる。`.claude/skills/` に配置するスキル定義の書き方と、スラッシュコマンドとの使い分けも紹介。

## 🌐 海外コミュニティ / Tips

- **[Getting Started with XcodeBuildMCP: Let AI Agents Build, Test, and Debug Your iOS Apps（sentry.io ワークショップ）](https://sentry.io/resources/xcodebuildmcp-workshop/)** — Sentry が公式ワークショップページを公開。XcodeBuildMCP の導入から Claude Code との連携、シミュレーター起動・テスト実行・LLDB デバッグまでを段階的にカバーする内容。XcodeBuildMCP を買収した Sentry が公式コンテンツとして整備した位置づけで、これまでコミュニティ頼りだった入門情報が一か所にまとまった。macOS 14.5+・Xcode 16.x+ が対象。

## 💡 今日のおすすめ実践 Tip

**Xcode 26.5 Enterprise Configuration で Claude Agent の接続先を Amazon Bedrock に切り替える**

社内ポリシーで Anthropic クラウドへの直接通信が難しいチームでも、Xcode 26.5 の Enterprise Configuration を使えば Amazon Bedrock 経由で Claude Agent を利用できる。Claude Code CLI の `settings.json` とは別に、Xcode Agent 専用の `settings.json` を所定のディレクトリに置くだけで設定が完了する。

**設定ファイルの場所（Xcode Agent 専用）:**

```bash
# Xcode Claude Agent の設定ディレクトリを作成
mkdir -p ~/Library/Developer/Xcode/CodingAssistant/ClaudeAgentConfig
```

**Bedrock 接続の設定例（settings.json）:**

```json
{
  "env": {
    "ANTHROPIC_API_KEY": ""
  },
  "mcpServers": {
    "XcodeBuildMCP": {
      "command": "/opt/homebrew/bin/xcodebuildmcp",
      "args": ["mcp"]
    }
  },
  "apiKeyHelper": "/usr/local/bin/get-bedrock-token.sh",
  "apiBaseUrl": "https://bedrock-runtime.ap-northeast-1.amazonaws.com/model"
}
```

**注意点:**
- `apiKeyHelper` に指定するスクリプトは AWS STS から一時クレデンシャルを取得して Bedrock の認証トークンを返す構成にするのが一般的（社内 IAM ロールに合わせて調整）
- Xcode Agent の PATH は制限されているため、`xcodebuildmcp` は Homebrew のフルパス（`/opt/homebrew/bin/`）で指定すること。`npx -y` 形式は PATH 解決に失敗することがある
- この設定は Xcode Agent 専用であり、通常の Claude Code CLI（`~/.claude/settings.json`）とは独立している。両方を使う場合は別々に管理が必要
- Xcode 26.5 以降が必要。Xcode 26.3・26.4 では Enterprise Configuration は利用不可
