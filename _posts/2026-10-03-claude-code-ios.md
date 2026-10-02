---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-10-03"
date: 2026-10-03 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- **[v2.1.288（2026-10-02）リリース](https://github.com/anthropics/claude-code/releases/tag/v2.1.288)** — **クラウドセッションに `gh api` を組み込み**：GitHub CLI（`gh`）がインストールされていないクラウドセッション環境でも `gh api repos/{owner}/{repo}/...` 形式の REST API 呼び出しが組み込みコマンドとして使えるように。iOS CI/CD フロー（App Store Connect への TestFlight ビルド確認・fastlane match の SSH キー操作など）でクラウドセッションから GitHub API を直接叩けるようになる。また、**`claude plugin install` が GitHub SSH キーなしの macOS/Linux でも動作するよう修正**（MobileBuildMCP など GitHub ソースのプラグインを SSH キー設定なしで導入できる）。その他、プラグイン LSP サーバーが `initializationOptions` / `settings` に文字列プレースホルダーをそのまま渡していたバグを修正、プラグインリロード中にバックグラウンドセッションが終了する問題を修正。

## 🛠 GitHub の動き

- **[Issue #96904（2026-09-24 作成 / 2026-10-02 CLOSED）— Intel Mac で iOS Simulator パネルが黒画面になる（ストリームは正常・ネイティブ Simulator は動作）](https://github.com/anthropics/claude-code/issues/96904)** — Intel Mac（デスクトップアプリ）で Claude Code の iOS Simulator パネルに映像が表示されず真っ黒になるバグが 2026-10-02 にクローズ。ネイティブ Simulator.app 自体は正常動作しており、ストリーミング自体も健全だったが Claude Code のデスクトップアプリ側のレンダリング問題が原因。ラベル: `bug`, `platform:macos`, `area:desktop`。Apple Silicon 以外の環境で Simulator パネルを使っていたユーザーは最新版で確認を。

- **[Issue #98520（2026-09-30 作成 / OPEN）— デスクトップアプリの iOS Simulator パネルが Mac のクリップボードを共有していない（デバイスへの貼り付けが不可）](https://github.com/anthropics/claude-code/issues/98520)** — Claude Code デスクトップアプリに埋め込まれた iOS Simulator パネルで、Mac 側でコピーしたテキスト・URL・コードをシミュレーター上のテキストフィールドに貼り付けできない問題。ネイティブ Simulator.app では正常に動作するため Claude Code のパネル実装固有の制限と判定。ラベル: `bug`, `has repro`, `platform:macos`, `area:desktop`。

## 📝 日本語コミュニティ

- 該当なし（2026-10-03 時点で Zenn・Qiita の新規 Claude Code × iOS / Swift / Xcode 記事は確認できず）

## 🌐 海外コミュニティ / Tips

- **[autoapp-toolkit — 1 つの Claude Code エージェントで iOS ポートフォリオアプリを複数管理するオーケストレーションフレームワーク（GitHub: jiejuefuyou/autoapp-toolkit）](https://github.com/jiejuefuyou/autoapp-toolkit)** — Claude Code の 5 時間トークンウィンドウをまたぐ問題に対処するため、`MEMORY.md` / `state.yml` / ADR（Architecture Decision Records）ログ / クロスリポジトリ検証スクリプト / App Store Connect API 自動化などを組み合わせたオーケストレーション層。fastlane match・XcodeGen・Maestro（UI テスト）・GitHub Actions を統合し、複数の iOS アプリを横断して 1 つのエージェントに継続して作業させる構成。コンテキストを外部ファイルに永続化することで、再開時にも以前の決定を再議論せずに作業を継続できる。

## 💡 今日のおすすめ実践 Tip

**「v2.1.288 の組み込み `gh api` をクラウドセッションで使い、TestFlight ビルド状況を確認する」**

v2.1.288 からクラウドセッション環境に GitHub CLI が未インストールでも `gh api` サブコマンドが組み込みで利用できます。iOS 開発で特に便利なのが、App Store Connect 連携のワークフローを Claude にそのまま引き渡せる点です。

```bash
# クラウドセッション上で GitHub Releases（TestFlight 配布用タグ）を確認
gh api repos/{owner}/{ios-app-repo}/releases/latest

# fastlane match 用の Deploy Key 一覧を確認（SSH キー設定不要）
gh api repos/{owner}/{match-repo}/keys

# GitHub Actions ワークフローの最新実行結果（CI/CD ステータス確認）
gh api repos/{owner}/{ios-app-repo}/actions/runs --jq '.workflow_runs[0] | {status, conclusion, created_at}'
```

クラウドセッションでの CLAUDE.md に以下のように記載しておくと、Claude がビルド状況の確認を自動的に `gh api` 経由で行うようになります：

```markdown
## CI/CD 確認方法（クラウドセッション）
gh CLI が使えない場合は `gh api repos/OWNER/REPO/...` を使ってください。
TestFlight ビルド状況: `gh api repos/OWNER/REPO/actions/runs` で確認。
```

`gh api` の `--jq` オプションで必要なフィールドだけ抽出すると、大量の JSON をコンテキストウィンドウに流し込まずに済みます。
