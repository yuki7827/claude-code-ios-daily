---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-07-29"
date: 2026-07-29 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

該当なし（直近リリースは v2.1.220（2026-07-25）で 2026-07-26 の記事で取り上げ済み）

## 🛠 GitHub の動き

- [Issue #82087 — Mobile: エージェントのターン中ナレーションがツールアクティビティチップに折りたたまれ、最終メッセージがコンテキスト欠落に見える（OPEN / 2026-07-28）](https://github.com/anthropics/claude-code/issues/82087) — iOS アプリで Claude Code のセッションを閲覧中、エージェントが複数ツールを呼ぶ長いターンを実行した際に、ツール呼び出し間の短いナレーション（「バグを発見」「Manifest の orientation フィールドが原因」など）が「Ran 6 commands +3」のアクティビティチップに折りたたまれる問題。デスクトップでは全テキストが見えるのに、モバイルでは中間のナレーションが隠れたまま最終メッセージだけが届くため脈絡なく結論が飛び込んでくる体験になる。報告者のコメント：「会話を自分自身でしていて、最後の部分だけ送ってくれる感じ」。本日の実践 Tip で CLAUDE.md を使った回避策を紹介する。

- [Issue #79574 — Mobile Code タブがライブブリッジ + クラウドセッションのみを表示し、デスクトップ App セッションはアイドル後に消える（OPEN / 2026-07-20）](https://github.com/anthropics/claude-code/issues/79574) — `autoUploadSessions: true` を設定しても、iPhone の Code タブには「現在 Remote Control ブリッジが生きているセッション」と「クラウド（リポベース）セッション」しか表示されず、デスクトップアプリで起動して完了・アイドルになったセッションは Code タブから消える（Archived にも入っていない）。7/27 記事で取り上げた Issue #81466（自動アーカイブ）とは別バグで、アーカイブ OFF でも発生する。同日には Issue #78792（iOS Artifacts ビューに Claude Code アーティファクトが出ない）・Issue #75343（セッションタイトルのサーフェス差異）・Issue #78679（モバイルに Pinned セクションなし）も更新されており、モバイル ↔ デスクトップの同期信頼性に関わる問題群として Anthropic がまとめて把握しているとみられる。

## 📝 日本語コミュニティ

- [Xcode 26.3 で iOS 開発の AI 活用はどう変わるのか ── タップルでの導入方針とあわせて（CyberAgent Developers Blog）](https://developers.cyberagent.co.jp/blog/archives/62551/) — サイバーエージェント傘下タップルのエンジニアが、Xcode 26.3 の `xcrun mcpbridge` 公式 MCP サーバーを Claude Code と組み合わせた実務評価と社内導入方針をまとめた記事。「ビルド・テスト・Preview はエージェント任せ、コードレビューは人間が担当」という役割分担と、CLAUDE.md + `.claude/` のコードベース管理を導入の柱とした構成が参考になる。iOS 大規模チームが Claude Code を段階的に採用するロールモデルとして価値が高い。

- [Xcode 26.3 エージェント型コーディング入門 — Claude・Codex・MCP で開発を自動化（Qiita / kai_kou）](https://qiita.com/kai_kou/items/1b24ad62dde4c02ae4f0) — Zenn にも同内容が掲載（[zenn.dev](https://zenn.dev/kai_kou/articles/021-xcode-26-3-agentic-coding-guide)）。`xcrun mcpbridge` を起動して Claude Code から Xcode の MCP ツール（ビルド・テスト・Preview スナップショット・LLDB・ドキュメント検索）を呼び出す手順をステップバイステップで解説。「MCP ツール導入前後でエージェントの挙動がどう変わるか」を実例で比較しており、Xcode 26.3 + Claude Code を初めて試す際の出発点として最適。

- [コードを 1 行も書かずに 25,000 行の iOS アプリを作った話 ── Claude Code で完全 Vibe Coding（Qiita / john-rocky）](https://qiita.com/john-rocky/items/b5a24858e797c954b16f) — 11 日間（2026-02-15〜26）で 155 ファイル・25,000 行規模の iOS アプリを Claude Code だけで作り上げた体験記。SwiftUI + SwiftData + FoundationModels（Apple オンデバイス LLM）+ AVFoundation の構成を Claude Code が自律選択した点が興味深い。長期セッションで失敗しやすいパターン（コンテキスト汚染・過剰リファクタリング）への対処法も実践的。

## 🌐 海外コミュニティ / Tips

- [conorluddy/ios-simulator-skill（GitHub）](https://github.com/conorluddy/ios-simulator-skill) — Claude Code の iOS シミュレーター操作をラップするコミュニティ製スキル。xcodebuild のビルド・実行コマンドを Claude Code に最適化した形で再パッケージし、「ビルド → シミュレーター起動 → ログ確認 → 修正」のループをスラッシュコマンドで完結させる設計。XcodeBuildMCP より軽量で、シンプルなプロジェクトでの代替や XcodeBuildMCP との併用として使える。Xcode 27 beta + DeviceHub 対応は対応中（2026-07 時点）。

## 💡 今日のおすすめ実践 Tip

**「CLAUDE.md で最終メッセージを自己完結型にして iOS アプリからのモニタリング体験を改善する」**

Issue #82087（Mobile: ターン中ナレーションの折りたたみ）は Anthropic 側の UI 修正待ちですが、エージェント側の指示を調整することでモバイルでの読みやすさを大幅に改善できます。

CLAUDE.md に以下のような指示を追加してください：

```markdown
## モバイル閲覧対応

各ターンの最終メッセージは、中間のナレーションを読んでいなくても理解できるよう自己完結させる。
必ず以下の 3 点を最終テキストに含める：
1. 何を発見したか（例: 「Manifest の orientation フィールドが nil を返していた」）
2. どう対処したか（例: 「enum の case を追加し .portrait をデフォルトに設定」）
3. 結果（例: 「Unit test 全 pass・ビルド通過・コミット abc1234 を push 済み」）
```

この指示があると、アクティビティチップに折りたたまれた中間ナレーションを見ていなくても、iPhone の Code タブや通知で最終メッセージだけを読んで作業全体を把握できます。XcodeBuildMCP でビルドを走らせながら外出先で iPhone からモニタリングするワークフローと特に相性が良いです。

Issue #79574（Mobile Code タブのセッション消失）については、現状では `claude remote-control` デーモン経由で起動したセッション（`environment_id` が設定されるもの）のみが Code タブに安定して残ります。デスクトップで起動したセッションを後からモバイルで参照したい場合は、引き続き **Remote Control デーモン経由での起動を基本**とするのが安定策です（7/27 記事の Tip も参照）。
