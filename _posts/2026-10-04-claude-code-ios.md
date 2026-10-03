---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-10-04"
date: 2026-10-04 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- **本日時点（2026-10-04）で新バージョンのリリースは確認されていない** — v2.1.288（2026-10-02 リリース）が最新版。詳細は 10-03 の記事参照。なお、v2.1.287（10-01）で追加された **`/doctor prompt-audit`** コマンドについては前回記事で取り上げなかったため以下で補足する。このコマンドは CLAUDE.md・スキル・エージェント定義を解析して古いパターンや推奨されない設定を検出する。iOS プロジェクトで膨大な CLAUDE.md を運用している開発者は一度実行する価値がある（`/doctor prompt-audit` と打つだけ）。

## 🛠 GitHub の動き

- **[Issue #96949（2026-09-25 作成 / 2026-10-03 CLOSED）— スコープ付き・オーナー承認済みテストアカウントサインイン（iOS Simulator / Device Hub / ネイティブアプリ対応）](https://github.com/anthropics/claude-code/issues/96949)** — iOS アプリの QA フローで Claude にテストアカウントへのサインインを任せたい開発者からの Feature Request が 2026-10-03 にクローズ（実装相当として解決）。現状は Claude がパスワード入力を拒否するため、1Password autofill を使っても iOS Simulator・Device Hub・ネイティブアプリのサインイン画面では動作しないという課題があった。提案の要点は `settings.json` に **テスト用認証情報のオーナー定義 allowlist** を置き、モデルには値を渡さずクレデンシャル参照（`"credentialRef": "op://Dev/Staging test user"` 等）でドメイン・Bundle ID・シミュレーターをスコープ限定でサインインさせる仕組み。**モデルが認証情報の値を見ない**まま iOS Simulator 上の UI テストをヘッドレスで自動化できるようになる設計で、Closed ラベルが付いた。ラベル: `enhancement`, `area:security`, `area:desktop`, `platform:macos`。

## 📝 日本語コミュニティ

- 該当なし（2026-10-04 時点で Zenn・Qiita の新規 Claude Code × iOS / Swift / Xcode 記事は確認できず）

## 🌐 海外コミュニティ / Tips

- **[sim-set — プロジェクトスコープ付き iOS Simulator ネームスペーシング（Claude Code スキル）（GitHub: nbonatsakis/sim-set）](https://github.com/nbonatsakis/sim-set)** — 複数の Claude Code エージェントが同一マシン上で別々の iOS プロジェクトを並行開発する際、**シミュレーターデバイスをプロジェクト単位で分離する** Claude Code スキル。`autoapp-toolkit`（10-03 記事）同様に並行エージェント開発を前提とした周辺ツールの一つ。エージェント A がプロジェクト X のシミュレーターを使っている間にエージェント B がプロジェクト Y のシミュレーターを起動・消去しても干渉しない。Xcode 27 の Device Hub にも対応済み（2026-10 更新）。インストールは `git clone ... && ./scripts/install.sh` のみ。xcodebuild・Xcode・Device Hub との互換性を維持したまま各プロジェクトのシミュレーターセットを名前空間で管理する。

## 💡 今日のおすすめ実践 Tip

**「`/doctor prompt-audit` で iOS プロジェクトの CLAUDE.md を定期メンテナンスする」**

大規模 iOS プロジェクトで CLAUDE.md が育つにつれ、古くなったスキル参照・廃止されたコマンド・重複した指示が蓄積しやすくなります。v2.1.287 で追加された `/doctor prompt-audit` を定期的に実行することで、コンテキストの無駄なトークン消費を防げます。

```
# Claude Code セッション内で実行
/doctor prompt-audit
```

実行すると CLAUDE.md 内の以下のような問題を検出してくれます：

- 廃止された MCP サーバー名（例: `xcodebuildmcp` → `mobilebuildmcp` への移行漏れ）
- 参照先が存在しないスキル・エージェント定義
- 長大化したセクションで重複している指示
- バージョン固定のコマンドが最新 API と食い違っているケース

iOS 開発では `CLAUDE.md` に xcodebuild コマンド・fastlane レーン名・`simctl` のパスなどを書き込むことが多く、ツールチェインのバージョンアップで陳腐化しがちです。スプリントの開始前や Xcode メジャーアップデート後など、節目のタイミングで一度 `/doctor prompt-audit` を走らせる習慣をつけると CLAUDE.md が常に最新のチーム規約を反映した状態を維持できます。
