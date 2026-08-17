---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-08-18"
date: 2026-08-18 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- **Auto モードが Pro/Max/Team プランのデフォルトに（2026-08-14 適用）**（[Anthropic ブログ](https://claude.com/blog/auto-mode-default-in-claude-code) / [TechCrunch 報道 2026-08-09](https://techcrunch.com/2026/08/09/anthropic-is-turning-claude-codes-auto-mode-on-by-default/)）— v2.1.233 と同日（2026-08-14）に有効化。これまでファイル書き込みや Bash コマンドのたびに手動承認が必要だったが、**安全性分類器（safety classifier）が各ツール呼び出しを「不可逆・破壊的・スコープ外」かどうか自動判定する Auto モードがデフォルトに**なった。セッション中に 3 回連続ブロック、または合計 20 回ブロックされた場合は手動承認モードにフォールバックする。Anthropic の調査では人間レビュアーが危険なコマンドを捕捉できる割合が 13.6% であるのに対し、Auto モードの分類器は 89% を捕捉したとされる。また Auto モードを使うチームは PR を約 25% 多く出荷するというデータもある。**iOS 開発への影響**: XcodeBuildMCP の `build`・`clean`・`archive` 等のコマンドが自動承認で流れるようになるため、エージェントの流れが止まりにくくなる。一方、プロジェクトファイルの大量書き換えなど意図しない破壊操作は分類器が弾くため、安全性も維持される。以前のデフォルト（手動承認）に戻すには Shift+Tab で切り替えるか、設定で `"defaultMode": "normal"` を指定する。

## 🛠 GitHub の動き

- [Issue #81520 — macOS 27 beta: Desktop iOS Simulator パネルが BitmapStream でクラッシュ・SimulatorKit の配置先が Xcode 27 beta 4 で変更（CLOSED / 2026-08-17）](https://github.com/anthropics/claude-code/issues/81520) — 2026-08-17 にクローズ（別 Issue の重複として整理）。本 Issue は 2 つのバグを報告していた: ① **Bug 1（SimulatorKit の移動）** — Xcode 27 beta 4 が `SimulatorKit.framework` を `Contents/Developer/Library/PrivateFrameworks/` から `Contents/SharedFrameworks/` に移動したため、Claude Desktop のヘルパー（FBControlCore ベース）が旧パスに固定で依存しておりロードに失敗する。② **Bug 2（BitmapStream クラッシュ）** — SimulatorKit がロードできた場合でも `BitmapStream` スレッドで SIGABRT が発生し sidecar がクラッシュループする。重複クローズの統合先は引き続き公開中の [Issue #79991](https://github.com/anthropics/claude-code/issues/79991)（OPEN）で、そちらに Anthropic のコメントが集約されている。**公式の回避策（現時点）**: Xcode 27 beta のみインストールされている環境では `sudo xcode-select -s /Applications/Xcode.app/Contents/Developer` を実行して Xcode 26.x を隣にインストールし、26.x 側を xcode-select で指定することで iOS Simulator パネルが利用可能になる。根本修正はまだリリースされていない。

## 📝 日本語コミュニティ

- 該当なし（2026-08-15〜18 の範囲で確認できる新規記事なし）

## 🌐 海外コミュニティ / Tips

- [claude-code-ios-dev-setup（ApptitudeLabs / GitHub）](https://github.com/ApptitudeLabs/claude-code-ios-dev-setup) — iOS エンジニア向けに特化した Claude Code CLI セットアップガイド。XcodeBuildMCP・GitHub MCP・mcpbridge の三本柱による MCP 構成、Axiom が提供する 50+ の iOS 専用エージェントスキル（パフォーマンス・アーキテクチャ・アクセシビリティの自動監査付き）、Antoine van der Lee の Swift Concurrency / SwiftUI / Core Data スキルを組み合わせた実践的なセットアップを解説。PRD ドリブン開発テンプレート・extended thinking（ultrathink）の使い方・SwiftLint / EditorConfig / フック自動化まで含む。汎用 AI セットアップガイドと異なり「iOS 開発を主ユースケースとして設計されている」点が特徴。全投稿での初掲載。

- [Claude Code Auto Mode — 89% vs 13.6% Human Catch Rate（explainx.ai）](https://www.explainx.ai/blog/claude-code-auto-mode-default-pro-max-team-august-2026) — Auto モードのデフォルト化を iOS 開発者視点で解説した記事。Auto モードが「不可逆・破壊的・スコープ外」と判定する条件の解説と、`xcodebuild` 等の Xcode ツールに対して分類器がどう動くかの解説が参考になる。

## 💡 今日のおすすめ実践 Tip

**「Auto モードデフォルト化後の iOS 開発 CLAUDE.md 設定 — XcodeBuildMCP を安全かつスムーズに動かすために」**

Auto モードが Pro/Max/Team のデフォルトになったことで、XcodeBuildMCP 経由の `build`・`test`・`launch_simulator` 等が手動承認なしで流れるようになりました。これは開発スループット向上に直結する一方、**分類器が「破壊的操作」と誤判定するとエージェントが途中で止まる**ケースも想定されます。以下の CLAUDE.md 設定を追加することで、よくある誤検知を防ぎつつ安全を保てます。

**CLAUDE.md への追記例（iOS プロジェクト向け Auto モード設定）**

```markdown
## ビルド・テストの許可操作（Auto モード用）

以下の操作は本プロジェクトの通常作業です。安全です。承認不要で実行してください:
- XcodeBuildMCP の build / clean / test / archive ツール
- xcrun simctl でのシミュレータ起動・停止・スクリーンショット
- swift package resolve / update
- git add / commit（ステージングエリアのみ）

以下の操作は必ず確認を求めてください（破壊的なため）:
- git push / git reset --hard
- rm -rf を含む操作
- App Store Connect や TestFlight への直接デプロイ
- Keychain や証明書ファイルへの書き込み
```

**Auto モードのフォールバック挙動を把握しておく**

Auto モードは 3 回連続ブロック or セッション合計 20 回ブロックで手動モードに自動フォールバックします。長時間のビルドセッションで突然 Claude が確認を求め始めた場合は、このフォールバックが発動した可能性があります。

```bash
# 現在のモードを確認
claude --status

# Auto モードに戻す（セッション内）
# Shift+Tab で切り替え、または次のプロンプト先頭に指示:
# 「auto モードで続けてください」
```

**「3 ブロック連続」を避けるための指示テクニック**

分類器が弾く可能性のある操作（大量のファイル変更、ネットワークアクセスを伴う操作等）は、CLAUDE.md に「段階的に確認しながら進める」ポリシーを書いておくと、Claude 自身が安全ルートを選びやすくなります:

```markdown
## 段階的実装ポリシー

大規模な変更を行う場合:
1. まず変更内容のサマリーを日本語で提示する
2. 私が「進めて」と言ったら実装する
3. 一度の変更は最大 10 ファイルまでとする

ただし以下は確認なしで実行可:
- XcodeBuildMCP ツール全般
- テストファイルの作成・更新
- Swift ソースファイルの追加・編集（既存ファイルの削除は除く）
```

**既存の `"defaultMode": "autoAccept"` 設定との関係**

以前 Auto モードを手動で有効化していた場合（`.claude/settings.json` に `"defaultMode": "autoAccept"` を設定していた場合）は、すでに同等の設定が適用されているため変更不要です。新規にプロジェクトを始める場合は、上記の CLAUDE.md ポリシーを設定ファイルと組み合わせることで、チームメンバーが異なる Claude プランを使っていても一貫した動作が得られます。
