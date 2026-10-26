---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-10-27"
date: 2026-10-27 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Claude Code Changelog](https://code.claude.com/docs/en/changelog) — 2026-10-27 時点の最新は v2.1.310（2026-10-23）のまま。週明け時点で新規バージョンリリースなし。v2.1.310 の内容（Bedrock / Vertex / Foundry での Monitor ツール対応、Enterprise カスタムロールへの Cloud sessions 権限追加など）は 10/25 キャッチアップ参照。

## 🛠 GitHub の動き

- [#104627（2026-10-23 open）iOS Simulator ツールが Xcode 27 でタッチ / ボタン入力を注入できない（Simulator.app → DeviceHub.app 置換）](https://github.com/anthropics/claude-code/issues/104627) — Xcode 27 で Simulator.app が DeviceHub.app に置き換えられた結果、Claude Code の iOS Simulator ツールがタップ・ボタン押下を注入できなくなるバグ。Xcode 27 使用中で iOS Simulator パネルのインタラクション機能が動作しない場合はこの Issue を参照。
- [#103525（2026-10-19 open）iOS Simulator パネルが静止画面でも常時 60fps フル解像度でストリーミングする](https://github.com/anthropics/claude-code/issues/103525) — 画面が変化していない状態や表示を切った後も、シミュレーターパネルが 60fps フルスクリーン転送を継続する問題。Claude Code セッション中にシミュレーターを開いているだけで Mac の CPU / GPU 負荷が高くなる。
- [#103991（2026-10-21 open → 2026-10-24 closed）iOS Simulator パネルがホームボタン端末をノッチ付きフレームに横方向引き伸ばし](https://github.com/anthropics/claude-code/issues/103991) — 10/22 時点では open だった同 Issue がクローズ済みに。修正は次のパッチリリース（v2.1.311 以降）に含まれる見通し。iPhone SE / 8 など旧型端末のシミュレーター表示確認で誤ったフォームファクターが出ていた場合は改善が期待できる。

## 📝 日本語コミュニティ

- [AI に iOS アプリを実装させるとき、触らせてはいけないファイルと守り方（Qiita / yasu-ya）](https://qiita.com/yasu-ya/items/c5b28fe4781d96723b20) — Xcode 26.6 + Claude Code での実践をもとに「Claude Code に変更させると危険なファイル・設定」をまとめた記事（2026-10 公開）。CI 設定ファイルや CLAUDE.md 本体、Xcode の `.xcscheme` ・ `.xcconfig` など改変されると困るファイルを読み取り専用として Claude に伝える方法と、フック・パーミッション設定による保護手順を解説。

## 🌐 海外コミュニティ / Tips

- [Xcode 26.3 + Claude Agent — Model Swapping, MCP, Skills, and Adaptive Configuration（fatbobman.com）](https://fatbobman.com/en/posts/xcode-263-claude/) — Xcode 26.3 の Claude Agent を使い倒すための設定ガイド。プロジェクトごとにモデルを切り替える `modelPicker`、`xcrun mcpbridge` を Claude Agent から呼ぶ MCP 統合、Xcode 付属スキルのインストール手順、リポジトリ構造に応じて適応する CLAUDE.md の書き方を詳説。英語記事だが内容は Xcode 26.3 / Claude Code 双方のユーザーに有用。
- [Building iOS Apps with AI Agents: The Practitioner's Guide（blakecrosley.com）](https://blakecrosley.com/guides/ios-agent-development) — Swift / iOS での AI エージェント活用の総合実践ガイド。「エージェントが得意な作業（SwiftUI View・SwiftData モデル・ユニットテスト・リファクタリングなど 70〜80%）」と「苦手な作業（.pbxproj 直接編集・コード署名・Metal シェーダーデバッグ・App Store 申請）」を整理し、CLAUDE.md・フック・MCP の設定がプロンプトの工夫より重要と結論付けている。

## 💡 今日のおすすめ実践 Tip

**Xcode 27 の DeviceHub 問題は「Simulator ツール無効 + MobileBuildMCP の`run_simulator` 経由」で迂回する**

#104627 / #103525 で示された通り、Xcode 27 では Simulator.app が DeviceHub.app に完全移行しており、Claude Code の iOS Simulator ツール（タッチ注入・入力シミュレーション）が正常動作しないケースがある。Claude Code の修正版を待ちながら作業を止めないための暫定策を示す。

**1. Claude Code の iOS Simulator ツールを一時無効化**

`~/.claude/settings.json`（またはプロジェクトの `.claude/settings.json`）に下記を追加し、誤動作するツールの呼び出しを防ぐ:

```json
{
  "disabledTools": ["ios_simulator_tap", "ios_simulator_button"]
}
```

**2. MobileBuildMCP 経由でシミュレーターを起動・確認する**

Claude Code に次のようにシミュレーターの起動・スクリーンショット取得を依頼する（タッチ注入は使わず、目視確認に留める）:

```
MobileBuildMCP の launch_simulator ツールで iPhone 16 Pro を起動して、
take_simulator_screenshot で現在の画面を確認してください。
```

**3. インタラクションが必要な UI 検証は XCTest に委ねる**

タップ等のインタラクション検証は `XCUIApplication` を使った UI テストで代替し、Claude Code には `mobilebuildmcp run_tests` でテスト結果を確認させる方針が現時点では最も安定する。

Xcode 27 の DeviceHub 移行に伴う類似 Issue は #79991・#97826・#95466 など複数あり、多くは v2.1.310 以前の修正でクローズ済み。#104627 はまだ open のため、関連する公式修正は引き続き changelog を確認すること。
