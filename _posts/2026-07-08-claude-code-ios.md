---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-07-08"
date: 2026-07-08 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Claude Code v2.1.202（2026-07-06）— /config に「Dynamic workflow size」設定追加・/review を高速単一パスに戻す](https://code.claude.com/docs/en/changelog) — iOS 開発で Dynamic Workflows を使う際に影響する変更が 2 点。① `/config` に **Dynamic workflow size**（small / medium / large）設定が追加され、Claude が自律的に生成するワークフローのエージェント数の目安を指定できるようになった（上限強制ではなく advisory 指示）。大規模なリファクタリングを一気に回したいときは `large`、コンテキスト・コストを抑えたいときは `small` に設定することで、XcodeBuildMCP と組み合わせた iOS ビルドループの規模を調整しやすくなる。② `/review <pr>` が **単一パスの高速レビューに戻り**、多段エージェントレビューが必要な場合は `/code-review <level> <pr#>` を使う設計になった。また Remote Control（モバイル/Web）からコマンドが失敗するバグや、セッション resume が git worktree 数に比例して遅くなるバグも修正されている。

## 🛠 GitHub の動き

- [patrickserrano/skills — iOS デバッグ・Swift 並行処理・Liquid Glass を含む iOS/Swift 向けスキルバンドル](https://github.com/patrickserrano/skills) — `ios-debugger-agent`・`swift-concurrency-expert`・`swiftui-liquid-glass`・`swiftui-ui-patterns`・`swiftui-view-refactor`・`swiftui-performance-audit`・`release-app-store-changelog`・`native-app-profiling` など 10 スキルを収録した公開リポジトリ。`/plugin marketplace add https://github.com/patrickserrano/skills` でまとめてインストール後、個別スキル単位で呼び出せる。`ios-debugger-agent` はブレークポイント設定・LLDB コマンド生成・クラッシュログ解析を自動化、`swift-concurrency-expert` は Swift 6 strict concurrency / actor isolation / `@concurrent` 属性の正しい使い方を Claude に教え込む構成。dpearson2699 の 84 スキルバンドル（07-06 紹介）が iOS 26+ API 全般をカバーするのに対し、こちらはデバッグ・パフォーマンス・リリースフローに特化した実務寄りの選択。

- [kylehughes/apple-platform-build-tools-claude-code-plugin — Agent Skill + Subagent で Apple プラットフォームビルドを分離](https://github.com/kylehughes/apple-platform-build-tools-claude-code-plugin) — `xcodebuild`・`swift build`・`simctl`・`devicectl`・コード署名・プロファイリング・配布・バイナリツールなど xcrun エコシステム全体のリファレンスを Agent Skill に凝縮したプラグイン。メインのプログラミングエージェントから **Subagent** に xcodebuild コマンドを委任し、ビルドログを Subagent が内部処理して**結果だけをメインエージェントに返す**設計で、コンテキストウィンドウを大幅に節約できる。XcodeBuildMCP が「ツール単位のフラットな API」を提供するのに対し、本プラグインは「Agent Skill（知識層）＋ Subagent（実行層）」の 2 層構造でコンテキスト効率と柔軟性を両立させた点が異なる。

- [jamesrochabrun/AgentHub — Claude Code & Codex のセッションを束ねる macOS ネイティブ GUI](https://github.com/jamesrochabrun/AgentHub) — ClaudeCodeSDK（Swift）の作者 James Rochabrun が開発した macOS ネイティブのセッション管理アプリ。複数の Claude Code / Codex セッションをリアルタイムで並行監視し、**git worktree の作成・削除を UI から操作**、差分を split-pane で inline レビュー・編集できるほか、GitHub の PR / Issue ブラウザ・ファイルエクスプローラ・構文ハイライト付きエディタを内蔵。SwiftUI で実装された macOS アプリ自体が「Claude Code で iOS/macOS 開発する際のフロントエンド」として機能する点が独自の位置づけ。iOS プロジェクトの複数 feature ブランチを並行して Claude に担当させるワークフローに適合している。

## 📝 日本語コミュニティ

- [XcodeBuildMCP × Claude Code スキルシステムで、iOS ビルドを自動化する（Zenn / kyoichi）](https://zenn.dev/kyoichi/articles/claude-code-xcodebuildmcp-ios-build) — XcodeBuildMCP の 79 以上のツールを 16 ワークフローグループに整理し、Claude Code の **スキルシステム経由でサブエージェントに切り出す**構成を解説した記事。スキルシステムと組み合わせることでコンテキスト消費問題を解決する点に焦点を当てており、07-06 に紹介した「Xcode MCP × Claude Code プラグイン」（純正 MCP と XcodeBuildMCP のハイブリッド）とは視点が異なり、XcodeBuildMCP を単体でスキル化する実装寄りの解説。同著者による 2 記事の関係として、「ハイブリッド構成の概要」→「XcodeBuildMCP 単体のスキル化詳細」の順で読むと理解しやすい。

- [Xcode 26.3 の MCP とは何か — なぜ必要で、どう使うのか。7 つの Skills で実践する（Zenn & Qiita / dsgarage）](https://zenn.dev/dsgarage/articles/xcode-263-mcp-skills) — MCP を使わない場合の「コピペ中継者としての開発者」という問題を提示し、Xcode 26.3 の Apple 純正 MCP を 7 つのスキルに分解して実践する手順を解説。① 基本ビルドとテスト実行（結果保存）、② RenderPreview でスクリーンショット取得・AI がレイアウト問題・ダークモード問題を検出、③ DocumentationSearch で Apple 公式ドキュメント検索と自動修正、④ Xcode MCP の 9 つのファイル操作ツールの安全な使い方ガイドスキル、⑤ エラーを依存順に修正する分類スキル、など実務で即使えるスキル構成が紹介されている。著者は `dsgarage/XcodeMCP-Skills` として MIT ライセンスで全スキルを公開。

## 🌐 海外コミュニティ / Tips

- [XcodeBuildMCP v2.5.0 — tvOS・watchOS・visionOS の実機ビルド対応、v2.6.0 で UI 自動化を大幅強化](https://github.com/getsentry/XcodeBuildMCP/releases) — v2.5.0 で `build_device` ツールが **tvOS・watchOS・visionOS** に対応し、iOS だけでなくすべての Apple プラットフォームの実機ビルドを Claude Code から実行できるようになった。あわせて iOS シミュレータのソフトウェアキーボードの表示/非表示・Mac ハードウェアキーボードの接続/切断を切り替える**トグルツール**が追加され、キーボード操作を含む UI テストの自動化が容易になった。v2.6.0 では UI 自動化が大幅改善：操作結果の返り値が「確認済み」のみから**前景 UI のコンパクトなスナップショット（安定した要素参照 + 画面ハッシュ）**に変わり、エージェントが次のタップ先を直接選べるようになった。また `wait_for_ui` / `batch` / `drag` の 3 ツールも追加されている。

- [kylehughes/apple-platform-build-tools の Agent Skill + Subagent 設計について（Claude Plugin Hub）](https://www.claudepluginhub.com/plugins/kylehughes-apple-platform-build-tools) — 上記プラグインの設計思想が詳述されている解説ページ。「Agent Skill はビルドツールの理解に使い、Subagent は実際のビルド実行に使う」という役割分担と、メインエージェントのコンテキストを汚染しない Subagent 隔離の具体的な設定例が確認できる。

## 💡 今日のおすすめ実践 Tip

**「`patrickserrano/skills` の `ios-debugger-agent` + `swift-concurrency-expert` で Swift 6 データ競合を自律修正させる」**

Swift 6 strict concurrency への移行で頻出する **actor isolation エラー** や `Sendable` 準拠漏れを、Claude Code に自律修正させるフローを組む方法です。

**1. スキルのインストール**

```bash
# patrickserrano の skills バンドルをプラグインとして追加
claude plugin marketplace add https://github.com/patrickserrano/skills
claude plugin install ios-swift-skills

# 使用するスキルを確認
/skills list
```

**2. Swift 6 concurrency エラーの一括修正フロー**

```
# Claude Code への指示例
/swift-concurrency-expert
"MyApp ターゲット全体で Swift 6 strict concurrency を有効化して。
手順：
1. Package.swift（または Build Settings）で swiftLanguageVersions を .v6 に設定
2. xcodebuild でビルドして concurrency 警告・エラーを列挙
3. actor isolation エラーは actor 境界を整理して修正
4. Sendable 準拠漏れは @unchecked Sendable ではなく
   本来の Sendable 対応（値型への変換 or nonisolated 化）で修正
5. 修正後に再ビルドしてエラー 0 を確認してからコミット"
```

**3. クラッシュログを ios-debugger-agent に渡して原因特定**

```
# TestFlight のクラッシュレポートを貼り付けて解析
/ios-debugger-agent
"このクラッシュログを解析して根本原因と修正方法を提案して：
[クラッシュログ貼り付け]"
```

**Swift 6 移行の優先度判断表**

| エラー種別 | 推奨対応 | スキルでの自動化度 |
|-----------|---------|-----------------|
| actor-isolated property を非 async コンテキストから参照 | `nonisolated` or `await` に変更 | ★★★ 高（自律修正可） |
| `Sendable` 非準拠の型をクロージャにキャプチャ | 値型への変換 or `@Sendable` | ★★ 中（型によって人間判断） |
| `@MainActor` 漏れ（UI 更新が非メインスレッド） | `@MainActor` 追加 or `DispatchQueue.main` | ★★★ 高（パターンが明確） |
| 非同期シーケンスの Task キャンセル漏れ | `withTaskCancellationHandler` 追加 | ★ 低（ロジック理解が必要） |

`swift-concurrency-expert` スキルは Swift 6.2+ の `@concurrent` 属性や `isolated` パラメータなど新しい concurrency API も学習しているため、iOS 26 / Swift 6.2 プロジェクトでも精度高く動作します。
