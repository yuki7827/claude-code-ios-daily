---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-11-27"
date: 2026-11-27 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [MCP servers can now ship skills, run tasks, and more with Claude（Anthropic 公式、2026-11-24）](https://claude.com/resources/articles/skills-over-mcp-tasks-and-more) — **Skills over MCP がパブリックベータ公開**。MCP サーバーが MCP の Resources 機能を使ってスキルファイルを配布できるようになった。Claude Code 2.1.331 以降が必要で、Pro/Max/Ultra および Team（管理者有効化後）で利用可能（Enterprise は近日対応予定）。iOS 観点では、**MobileBuildMCP や Xcode MCP Bridge が今後スキルを自動配布できる**可能性がある。なお 11-26 記事で v2.1.332 の「MCP サーバーホスト型スキル（beta）」として紹介した機能は、この仕組みの Claude Code 側実装にあたる。

## 🛠 GitHub の動き

- 該当なし（本日時点で直近 5 投稿と重複しない iOS 関連の新規 Issue / PR は確認できず）

## 📝 日本語コミュニティ

- [Claude Code v2.1.332：MCPスキル（β）追加と凍結バグ大量修正——大企業AI担当が確認した変更点（Qiita / Sidejob）](https://qiita.com/Sidejob/items/d1fe8f95a810a5a2b959) — 企業 AI 担当者目線で v2.1.332 の変更点を整理した速報記事（2026-11-26）。iOS 観点でのポイントは「MCP ホスト型スキル（beta）が有効になると、Xcode の `xcrun mcpbridge` や MobileBuildMCP などの既存 MCP サーバーがスキルファイルを Resources 経由で配布できるようになり、`.claude/skills/` への手動配置が不要になる可能性がある」点。全 150 件の変更を含む大型リリースの要所が日本語でまとまっており、変更の背景まで掘り下げている。

- [Claude、Skills over MCP をベータ公開など、11/25 の AI ニュース（Qiita / xdaily）](https://qiita.com/xdaily/items/728ed748ab738b8aa9ac) — 2026-11-25 の AI 動向まとめ。Skills over MCP のほか、あるコーディングハーネス比較ベンチマークで Claude Code が最高スコアを記録したと報告。iOS 開発への接点は薄いが、Xcode MCP が Skills over MCP に対応した場合のスキル自動配布を見越す上で背景として有用。

## 🌐 海外コミュニティ / Tips

- [Anthropic Adds Skills Over MCP To Claude And Claude Code Beta（Dataconomy、2026-11-26）](https://dataconomy.com/2026/11/26/anthropic-adds-skills-over-mcp-to-claude-and-claude-code/) — Skills over MCP ベータの全体像を整理した英語解説。**サーバー側でスキルを更新すればクライアントへ自動伝播する**点が従来の手動スキルファイル配布との最大の違い。iOS チームとして注目すべきは「スキルの更新が Xcode バージョンアップと同期できる可能性」。現状 Apple の公式 Xcode プラグインが Resources を通じたスキル配布に対応するかは未発表だが、仕様上は可能になった。

- [Claude Skills vs MCP: I Replaced Half My iOS Stack（Level Up Coding）](https://levelup.gitconnected.com/anthropic-quietly-made-half-my-mcp-servers-obsolete-heres-the-6-skill-setup-that-replaced-them-32eb7e16f538?gi=29f8ff033ec3) — Skills over MCP の公開を受け、iOS 開発スタックの一部 MCP サーバーをスキルに置き換えた体験談。著者は 6 本のスキル構成に移行したと報告しており、スキルへの移行が有利なケース（コンテキスト注入用途・ドキュメント参照など）と MCP サーバーを維持すべきケース（ビルド実行・シミュレーター UI 操作など、外部プロセスとの実通信が必要な処理）の切り分け方が参考になる。

## 💡 今日のおすすめ実践 Tip

**Skills over MCP 時代の iOS スタック分割方針：「スキル = 知識、MCP = 実行」で整理する**

Skills over MCP のベータ公開で、スキルと MCP サーバーの役割分担がより明確になった。
iOS 開発スタックへの適用指針は次のとおり。

| 用途 | 推奨手段 |
|------|---------|
| ビルド・テスト・シミュレーター UI 操作 | MCP サーバー（MobileBuildMCP / xcrun mcpbridge）を維持 |
| Swift/SwiftUI コーディング規約・パターン | スキル（`SKILL.md` またはサーバー配布スキル）へ移行 |
| Apple ドキュメント参照・API ガイド | スキルへ移行（コンテキスト注入用途） |
| `.pbxproj` 保護フックなどの安全弁 | `PreToolUse` / `PreSkillShellCommand` フック（スキルではなく設定） |

MCP サーバーがスキルを配布するようになれば、`/plugin install xcode@claude-plugins-official` 1 コマンドで Xcode のスキルも含めてインストールが完結する。
現時点では手動配置が必要だが、MobileBuildMCP は `xcodebuildmcp init` コマンドで Claude Code 向けのスキルを自動インストールできる（2.1.0 以降）。

```bash
# MobileBuildMCP のスキルを自動インストール（Claude Code クライアントを自動検出）
npx xcodebuildmcp init

# Skills over MCP 対応後は、MCP サーバー設定さえあればスキルも自動で届くようになる予定
```
