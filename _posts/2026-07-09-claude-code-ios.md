---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-07-09"
date: 2026-07-09 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Claude Code v2.1.203（2026-07-07）— バックグラウンドセッション安定性の大幅改善・MCP roots/list 拡張](https://code.claude.com/docs/en/changelog) — iOS 開発者の夜間自律ビルドに直結する修正が多数。① **ログイン期限切れ警告を追加** — セッショントークンが切れる前に警告が表示されるようになり、`xcodebuild archive` などの長時間処理中にバックグラウンドセッションが強制終了される事態を事前に防げる。② **デーモンのセッショントークン失効からの自動回復** — 従来は `claude agents` からのアタッチ・応答・停止が永続的に効かなくなっていたが、失効を検知して自動回復するように修正。③ **git worktree 多数環境での「argument list too long」エラーを修正** — iOS プロジェクトで feature ブランチごとに worktree を切って Claude に並行作業させる運用でのクラッシュ原因を解消。④ **バイナリサイズと起動メモリを各 ~7MB 削減** — 遅延ロードの導入による改善で、CI コンテナ起動速度が向上。⑤ **macOS 誤った低メモリ検知による 15〜20 秒のスタールを修正**。⑥ **MCP `roots/list` にセッションの追加作業ディレクトリを含めるよう拡張** — Xcode MCP が iOS プロジェクトの全作業ディレクトリを正しく認識できるようになった。

- [Claude Code v2.1.204（2026-07-08）— SessionStart フックのヘッドレスセッション対応修正](https://code.claude.com/docs/en/changelog) — ヘッドレスセッション（リモート実行・CI）での SessionStart フックでフックイベントがストリームされない問題を修正。この不具合が原因でリモートワーカーがアイドル判定されて刈り取られるケースがあったため、GitHub Actions 等で Claude Code を使った iOS 自動化ワークフローを組んでいる場合は v2.1.204 へのアップデートを推奨。

## 🛠 GitHub の動き

- [greenstevester/fastlane-skill — Fastlane 全工程を自然言語で操作する 5 スキルバンドル](https://github.com/greenstevester/fastlane-skill) — iOS/macOS アプリのリリース自動化に特化した Claude Code スキル集。`.pbxproj` を自動解析して Bundle ID・Team ID を取得し、Fastlane の設定ファイルを生成する `setup`、Match による共有コード署名管理の `certs`、App Store スクリーンショット自動生成の `snapshot`、TestFlight アップロードの `beta`、App Store 申請の `release` の 5 スキルで構成される。「Fastlane のドキュメントを読まなくても `Set up Fastlane for my iOS app` と言うだけで動く」設計が特徴で、初回セットアップ 2〜3 時間の節約を謳っている。07-05 に紹介した MCPmarket の [Fastlane iOS Beta Release Skill](https://mcpmarket.com/tools/skills/fastlane-ios-beta-release) が TestFlight 単機能に特化しているのに対し、本リポジトリはリリースサイクル全体をカバーする点が異なる。`claude plugin marketplace add greenstevester/fastlane-skill` でインストール可能。

## 📝 日本語コミュニティ

- [【第1弾・第2弾】VS Code の Claude Code で「iOS コードレビュー → 修正 → Xcode ビルド確認（→ テスト項目作成）」を自動化する（Qiita / 4q_sano）](https://qiita.com/4q_sano/items/f29dba5fa92958850a32) — VS Code 上の Claude Code と `xcrun mcpbridge`（Xcode MCP）を組み合わせて、`git diff` から始まる iOS コードレビュー・自動修正・Xcode ビルド確認のループを自動化する手順を解説したシリーズ（第1弾: 2026-04-17）。[第2弾](https://qiita.com/4q_sano/items/effe51de5e9654777f1e)ではビルド成功後にテスト項目書を自動生成するところまで拡張されており、「Xcode を開かずに VS Code だけで iOS 開発の review → fix → build → test-spec サイクルを回す」フローが完成する。xcrun mcpbridge の接続設定と、MCP を通じた Xcode への Scheme 指定回避策（事前に Xcode 側で正しい Scheme を選択しておく）が具体的に示されている。著者 4q_sano は「Claude Code を暴走させず実務の仕組みにするまでの全体地図」として 9 ステップ 36 記事のシリーズを公開しており、iOS 特化記事が複数含まれる。

- [Claude Code のスキルで個人開発アプリの iOS リリースを楽にした話（Qiita / _it_）](https://qiita.com/_it_/items/45127d2e41cafff8477c) — React Native + Expo（EAS）で開発した iOS アプリのリリース作業を 2 つのカスタムスキルに凝縮した実例紹介（2026-06-19）。`/myapp-release-draft <version>` スキルは develop → main の PR 作成・バージョン番号更新・GitHub Release 草稿生成を自動化し、`/myapp-deploy` スキルはリント・型チェック → `eas build --platform ios --profile production --auto-submit --non-interactive` による本番ビルドと App Store 提出 → ストア掲載テキスト生成までを一気に処理する。プロジェクト固有の処理を Claude Code スキルとして切り出すことで「次回のリリースでも同じ品質のフローを再現できる」メリットを強調しており、greenstevester/fastlane-skill のような汎用スキルと違い、プロジェクト専用スキルを自作するパターンの参考になる。

## 🌐 海外コミュニティ / Tips

- [Claude Code for iOS: Shipping a Real App in 18 Days（Nodes and Edges / theyawns.com, 2026-06-24）](https://theyawns.com/2026/06/24/claude-code-for-ios-shipping-a-real-app-in-18-days/) — 最初のコミットから 18 日間でスコアリングアプリを App Store に公開した実録。TestFlight のパブリックリンク経由でベータユーザーからフィードバックを集めつつ App Store 審査を通過したフローが時系列で追える。Claude Code に任せた部分（SwiftUI ビュー・ロジック・テスト生成）と人間が判断した部分（UX の方向性・App Store 審査準拠確認）の分担が率直に語られており、「Claude Code は iOS 開発の初速を大幅に上げるが、App Store のガイドライン確認やベータユーザーの声をどう反映するかは人間の設計センスが依然重要」という結論が参考になる。

- [XcodeBuildMCP — `xcodebuildmcp upgrade` の更新チェック改善（GitHub Releases）](https://github.com/getsentry/XcodeBuildMCP/releases) — v2.6.2 で `xcodebuildmcp upgrade` コマンドがパッケージマネージャのキャッシュではなく **GitHub の最新リリースを正規の更新ターゲット**として参照するよう修正された。複数バージョンをまたぐアップグレードでは正しいバージョンのリリースノートが表示されるようになり、更新内容の把握が容易になった。`xcodebuildmcp setup` のプラットフォーム選択型ウィザードも整備されており、iOS / tvOS / watchOS / visionOS / macOS を冒頭で選ぶと対応するシミュレータ選択ステップや推奨ワークフローが自動的に絞り込まれる。

## 💡 今日のおすすめ実践 Tip

**「v2.1.203 の worktree + バックグラウンドセッション修正を活かした、iOS 並行 feature ビルドの夜間自律運用」**

v2.1.203 で修正された複数の不具合は、iOS プロジェクトで **複数の git worktree を使って Claude に feature ブランチを並行作業させる**ときに特に効いてきます。以下は具体的な設定例です。

**1. プロジェクトへの worktree セットアップ**

```bash
# main ブランチのあるリポジトリで
git worktree add ../MyApp-feature-A feature/liquid-glass-redesign
git worktree add ../MyApp-feature-B feature/swift6-migration
git worktree add ../MyApp-feature-C feature/widget-kit-live-activity
```

**2. 各 worktree で Claude Code エージェントを起動**

```bash
# ターミナルタブを分けて起動（または tmux）
cd ../MyApp-feature-A && claude agents start --name "LiquidGlass"
cd ../MyApp-feature-B && claude agents start --name "Swift6"
cd ../MyApp-feature-C && claude agents start --name "LiveActivity"
```

**3. CLAUDE.md に XcodeBuildMCP を MCP として登録しておく**

```json
// .claude/settings.json（プロジェクトルート）
{
  "mcpServers": {
    "xcodebuild": {
      "command": "npx",
      "args": ["-y", "@sentry/xcodebuildmcp"]
    }
  }
}
```

**v2.1.203 で安心できるようになったこと**

| 以前の問題 | v2.1.203 後の挙動 |
|-----------|-----------------|
| worktree が多いと「argument list too long」でクラッシュ | 修正済み・3 worktree 以上でも安定稼働 |
| デーモントークン失効でバックグラウンドセッションが無応答 | 失効を検知して自動回復 |
| macOS 誤低メモリ検知で 15〜20 秒フリーズ | 修正済み |
| ログイン切れに気づかず朝起きたら全セッション停止 | 事前警告を表示 |
| ヘッドレス CI で SessionStart フックが無音で失敗 | v2.1.204 で修正済み |

**夜間ビルドのコスト見積もり（目安）**

- 3 worktree × Claude Sonnet 5 で並行実行：1 セッション 30〜50 分想定
- 朝起きたら `claude agents list` で各ブランチの進捗確認
- 問題があった worktree だけ `claude agents attach <name>` で状況確認

`git worktree` を活用した iOS 並行開発は AgentHub（07-08 紹介）のような GUI ツールと組み合わせると、差分確認・worktree 操作・セッション監視が一画面で管理できてさらに便利です。
