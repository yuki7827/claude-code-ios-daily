---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-11-06"
date: 2026-11-06 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Claude Code 公式 Changelog](https://code.claude.com/docs/en/changelog) — 2026-11-05・06 時点で新規リリースは確認できず（最新は v2.1.318 継続）。ただし **Opus 4.8 fast mode が 2026年11月30日に廃止予定**（Claude Code v2.1.318 リリースノートに明記）。Xcode Cloud や GitHub Actions など iOS CI パイプラインで `--fast` フラグや `model: "claude-opus-4-8"` のファストモードを利用しているスクリプトは、**11月30日までに Opus 5.5 へ移行しないと処理が止まる**。`model: "claude-opus-5-5"` へ切り替え、または fast mode フラグを外すこと。

## 🛠 GitHub の動き

- 該当なし（2026-11-06 時点で直近 5 件に未掲載の新規 iOS / Xcode / Swift 関連 Issue・PR は確認できず）

## 📝 日本語コミュニティ

- [Claude Code に iOS シミュレータの動作確認まで自走させる — FlowDeck と AXe で作る自動 UI 検証（Qiita / suzuhiroruri）](https://qiita.com/suzuhiroruri@github/items/5a1560b36e9ee8411f5b) — Xcode 27.2 Beta 環境での実機レポート。FlowDeck の `flowdeck ui simulator screen --json` でアクセシビリティツリーを JSON として取得し、Claude Code がタップ・スワイプを決定、AXe CLI で実行してスクリーンショット確認、差異があれば再試行するループを構築。スクリーンショット画像をモデルに渡すより、アクセシビリティツリー（JSON）で渡す方がトークンコストが低く、座標依存のエラーも出にくいという実測比較も含まれる。

- [XcodeBuildMCP × Claude Code スキルシステムで、iOS ビルドを自動化する（Zenn / kyoichi）](https://zenn.dev/kyoichi/articles/claude-code-xcodebuildmcp-ios-build) — Claude Code のスキル機能（`.claude/skills/`）を使い、XcodeBuildMCP の各ツールにラップして「build」「test」「preview」をワンコマンドで呼べるようにする構成を紹介。xcworkspace と xcodeproj の自動検出ロジックや、`--destination` フラグの組み立てをスキル内で吸収することで、Claude が毎回 `xcodebuild` のオプションを推測する無駄を省く設計が詳細に解説されている。

- [iOS アプリ開発に役立つ Agentic Coding 集（Zenn / mtfum）](https://zenn.dev/mtfum/articles/ios_agentic_coding) — SwiftUI・TCA・SwiftData を使ったプロジェクトに Claude Code を組み込む際のプラクティス集。①ドメインモデルを先に固め Claude Code にインフラ・UI を任せる分業方針、② SPM のパッケージグラフを変えさせない制約の CLAUDE.md への記述例、③Preview Provider の自動生成に Claude Code を使うパターンなど、実務向けの Tips が整理されている。

## 🌐 海外コミュニティ / Tips

- [Two MCP Servers Made Claude Code an iOS Build System（blakecrosley.com）](https://blakecrosley.com/blog/xcode-mcp-claude-code) — XcodeBuildMCP（getsentry 版）と Xcode 27 純正 MCP の2本立てで iOS ビルドシステムを構成した実践レポート。XcodeBuildMCP はビルド・テスト・シミュレーター起動など CLI 操作に強く、Xcode 純正 MCP は SwiftUI プレビューとコードアクション（クイックフィックスの自動適用）に強い、という使い分けが具体的なツール名と共に説明されている。シミュレーターを Xcode を起動せずに動かすには `SIMCTL_CHILD_TERM_ON_APP_CRASH=1` 環境変数が必要な点も補足されている。

- [Three ways to close the iOS agent loop（flowdeck.studio）](https://flowdeck.studio/blog/2026/04/01/three-ways-to-close-the-ios-agent-loop/) — 「ビルド → インストール → 検証」のエージェントループを閉じる3アプローチを比較：① MCP サーバー経由（高機能・高コスト）、② CLI ツール（AXe / simctl）をシェルコマンドとして呼ぶ（低コスト・柔軟性あり）、③ FlowDeck の UI コマンド（JSON アクセシビリティツリー取得で中間コスト）。iOS CI で自動化するには②が最も設定コストが低く、座標ではなく `accessibilityIdentifier` を使えば変更に強いというまとめが役立つ。

## 💡 今日のおすすめ実践 Tip

**アクセシビリティツリー（JSON）でシミュレーターを操作するとトークンコストと精度が両立する**

Claude Code に iOS シミュレーターの操作を任せるとき、スクリーンショット画像を渡すのではなく AXe のアクセシビリティツリー JSON を渡すことで、トークンコストの削減と操作精度の向上が同時に得られる。

```bash
# AXe で現在のシミュレーター画面のアクセシビリティツリーを JSON 取得
axe dump --format json --app <bundle-id>
```

取得した JSON を Claude Code に渡すと、ボタン名・アクセシビリティラベル・フレーム情報などが構造化テキストで伝わるため、Claude が「何かをタップすべきかどうか」を座標でなく意味で判断できる。操作は同じく AXe で実行する：

```bash
# accessibilityIdentifier でボタンをタップ（座標より安定）
axe tap --app <bundle-id> --identifier "loginButton"
```

CLAUDE.md に次のルールを追記しておくと、Claude がスクリーンショット不要のループを維持しやすくなる：

```markdown
# シミュレーター操作
- スクリーンショットを読ませるより先に `axe dump --format json` でアクセシビリティツリーを確認すること
- タップ操作は座標でなく `--identifier` を使うこと
- アクセシビリティ ID は `AccessibilityIdentifier_<ファイル名>` の命名規則で統一すること
```
