---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-08-07"
date: 2026-08-07 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- **v2.1.223（2026-08-06）リリース**（[Claude Code 変更ログ](https://code.claude.com/docs/en/changelog)）— iOS 開発者向け主要変更: ① **`/teleport` ヒントをクラウドセッションに追加** — `claude.ai/code` のブラウザセッションや Remote 環境で作業中、`claude --teleport <session id>` とローカル Mac で実行するとセッションを丸ごと手元に引き継ぐことができる。XcodeBuildMCP を使うには macOS 上の Claude Code が必要なため、「ブラウザで設計・指示 → ローカル Mac で XcodeBuildMCP ビルド」の切り替えがワンコマンドに; ② **Bash パーミッション承認ダイアログの改善** — タブや invisible Unicode でコマンドの一部を承認ダイアログから隠せていたバイパス手法を 2 件修正。XcodeBuildMCP スクリプトや fastlane 呼び出しを含むプロジェクトでの実行承認がより正確になる; ③ **`/review` が `/code-review` のエイリアスに変更、`/code-review ultra` でクラウドレビューが可能**に — PR レビュー前の Swift コードチェックをクラウドで実行できるようになった; ④ ワークフロースクリプトが dynamic `import()` でサンドボックス外コードを実行できた問題の修正。

## 🛠 GitHub の動き

- [Issue #502 — XcodeBuildMCP: scaffold-ios が生成したプロジェクトで XCLocalSwiftPackageReference が欠如、最初のビルドが "Missing package product" で失敗（OPEN / getsentry/XcodeBuildMCP / 2026-08-02）](https://github.com/getsentry/XcodeBuildMCP/issues/502) — `xcodebuildmcp 2.6.0` + `Xcode 26.4` 環境で `xcodebuildmcp project-scaffolding scaffold-ios` を実行すると、生成された `project.pbxproj` に `XCLocalSwiftPackageReference` セクションが丸ごと欠如しており、初回ビルドで "Missing package product 'XxxxxFeature'" が出て進めない。報告者が検証済みの修正: ① `XCLocalSwiftPackageReference` オブジェクトを追加、② 各 `XCSwiftPackageProductDependency` に `package = <ref-id>` を付与、③ ターゲットの `packageReferences` に参照を追加。Issue #503 と同一環境・同日報告で、scaffold-ios テンプレートの複合的な破損が浮かんでいる。

- [Issue #503 — XcodeBuildMCP: scaffold-ios が生成したスキームに存在しない .xctestplan を参照、テストが "Tests cannot be run" で失敗（OPEN / getsentry/XcodeBuildMCP / 2026-08-02）](https://github.com/getsentry/XcodeBuildMCP/issues/503) — 同一の scaffold-ios 生成スキームが `container:<Name>.xctestplan`（未生成）と `container:<Name>/<Name>.xctestplan`（生成済み）の 2 つの TestPlanReference を持つが、Xcode は 1 件でも参照不正があるとテストアクション全体を拒否する仕様のため、正常なプランしかなくてもテストが実行できなくなる。**修正: 不正な `container:<Name>.xctestplan` の TestPlanReference を削除するだけ。**報告者の確認では、この変更後に Swift Testing 2 件 + UI テスト 1 件が全通した。Issue #502 と合わせ、`scaffold-ios` に Claude Code を使った iOS プロジェクト初期化を任せている場合は v2.6.0 系での動作確認が必要。

- [Issue #78792 — 公開した Claude Code Artifact が iOS モバイルアプリのアーティファクト一覧に表示されない（OPEN / 2026-07-18、最終更新 2026-08-06）](https://github.com/anthropics/claude-code/issues/78792) — Claude Code セッションから Artifact ツールで公開したページ（`claude.ai/code/artifact/<id>`）が Web・デスクトップアプリでは見られるのに、iOS アプリのアーティファクトビューにはリストされない問題。デスクトップアプリにはグローバルアーティファクトビューと Code アーティファクトビューの 2 か所に表示されるが、モバイルには Code セクション自体がない。アカウント共有なのに iOS だけ見えないのは意図的でなく一貫性の問題だと報告者は指摘。**回避策: 公開済みの `claude.ai/code/artifact/<id>` URL を Safari に直接貼り付ける**（サインイン済みセッションが必要）。08-02 記事の Issue #83174（iPhone でアーティファクトリンクが開けない）と並ぶモバイル UX の継続課題。

## 📝 日本語コミュニティ

- [SwiftUI × Claude Code で小規模事業者向け iOS アプリを MVP から App Store 申請まで全自動で作った話（Qiita / sorabcjanne1 / 2026-06-30）](https://qiita.com/sorabcjanne1/items/f0f6bc44c91d38d5e9c2) — 単一の指示書ドキュメントを起点に、MVP 実装 → UX 改良 → 機能拡張 → QA → ASO → App Store Connect API 経由の提出まで Claude Code に全通し。成果物は Swift ファイル約 70 本・7,000 行超・XCTest 162 件（全件パス）。SwiftData / PDFKit / EventKit / WidgetKit / AppIntents / Swift Charts / PencilKit / LocalAuthentication まで活用。人間の手作業は App Group 作成・App レコード作成・最終審査提出の 3 点のみ。「Claude Code がどこまでやってくれるかの天井」を確かめるのに適した実験記録。

## 🌐 海外コミュニティ / Tips

- [Issue #492 — XcodeBuildMCP: ビルドが "Planning" で永久ハング — stdout pipe デッドロックが孤立 MCP サーバー複数起動で増幅（OPEN / getsentry/XcodeBuildMCP / 2026-07-24）](https://github.com/getsentry/XcodeBuildMCP/issues/492) — `simulator build` や `device build` が Planning フェーズで止まり MCP ツール呼び出しが戻らなくなる問題。根本原因は宛先解決ワーニングが stdout に大量出力され、Node の pipe buffer（約 64 KB）が満杯になり `xcodebuild` の `write()` がブロックするパイプデッドロック。エージェントセッション終了後も `xcodebuildmcp` プロセスが残る（孤立プロセス）ため、複数世代が競合してデッドロック発生確率が上がる。**暫定回避策: ビルド前に `ps aux | grep xcodebuildmcp` で旧プロセスを `kill` → `xcodebuildmcp purge` で再起動。詰まったら Mac 再起動が最確実。**根本修正の提案方向: ① ビルドウォッチドッグ追加（進捗なし N 分でプロセス kill）、② パイプの常時ドレイン確保（sync `writeSync` から async stream へ変更）、③ シングルインスタンス強制。

## 💡 今日のおすすめ実践 Tip

**「XcodeBuildMCP 2.6.x の scaffold-ios バグを踏まずに Claude Code で iOS プロジェクトを生成する暫定フロー（Issue #502/#503 対策）」**

Issue #502 と #503 が示す通り、`xcodebuildmcp 2.6.0` の `scaffold-ios` コマンドが生成するプロジェクトは **そのままではビルドもテストも失敗**します（欠損したパッケージ参照と壊れたスキーム）。最新 v2.7.0 でも同スキャフォールドを用いる場合は同様の問題が残る可能性があるため、以下の確認フローを CLAUDE.md に追記しておくと、Claude Code が scaffold-ios を実行した直後に自動チェックできます。

**CLAUDE.md に追記するチェックルール**

```markdown
## scaffold-ios 実行後の必須チェック（Issue #502/#503 対応）

scaffold-ios でプロジェクトを生成した場合、ビルド前に以下を確認すること:

1. project.pbxproj に XCLocalSwiftPackageReference が存在するか確認
   ```
   grep -c "XCLocalSwiftPackageReference" <ProjectName>.xcodeproj/project.pbxproj
   ```
   → 0 が返ったらパッケージ参照が欠落している (Issue #502)

2. スキームの TestPlanReference が生成済みファイルのみを指しているか確認
   ```
   grep "TestPlanReference" <ProjectName>.xcodeproj/xcshareddata/xcschemes/<ProjectName>.xcscheme
   ```
   → container:<ProjectName>.xctestplan (スラッシュなし) があれば不正参照 (Issue #503)

いずれかが問題なら、Issues #502/#503 の workaround を適用してからビルドすること。
```

**Claude Code に自動修正させるプロンプト例**

scaffold-ios 実行後にそのまま以下を貼り付けると、上記 2 問題の有無を診断して自動修正まで試みます。

```
scaffold-ios で生成したプロジェクトが Issue #502（XCLocalSwiftPackageReference 欠如）と
Issue #503（不正 TestPlanReference）の影響を受けていないか確認し、
問題があれば各 Issue に記載の workaround を適用してください。
```

Issue がクローズされ scaffold-ios テンプレート自体が修正された段階でこのルールは不要になります。XcodeBuildMCP のリリースノートを確認して削除してください。
