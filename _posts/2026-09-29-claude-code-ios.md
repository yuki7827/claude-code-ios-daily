---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-09-29"
date: 2026-09-29 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- **[v2.1.284（2026-09-28）リリース](https://github.com/anthropics/claude-code/releases/tag/v2.1.284)** — **Claude Sonnet 5.5 がデフォルト Sonnet モデルに昇格**（1M コンテキスト、$2/$10/Mtok、キャッシュ読み込み $0.20/Mtok）。`/mcp reconnect all` コマンドで接続失敗または認証待ちのすべての MCP サーバーを一括再試行できるようになった（MobileBuildMCP 等が切断された際に便利）。使用状況・ステータスラインにドル換算の消費額を表示（例: `$271.40 / $500.00 spent this month`）。Auto モードで作業ディレクトリ外の読み取りを許可する際に「今回だけ許可、次回も確認する」オプションが追加。

## 🛠 GitHub の動き

- **[Issue #97734（2026-09-28 作成 / OPEN）— iPad/iPhone: ローカル（Remote Control）セッションとクラウドセッションが見た目で区別できない](https://github.com/anthropics/claude-code/issues/97734)** — iPad/iPhone の Code タブでは、Remote Control 経由のローカルセッションとクラウドセッションがほぼ同一のレイアウトで表示され、タイトルバーに `<repo>` vs `<repo> · <environment>` が表示される程度の違いしかない。**クォータ消費先**（ローカルは週 5 時間制限・クラウドはクラウドクレジット）と**ファイルアクセス範囲**（ローカルはローカルファイル・DB・dev サーバーにアクセス可、クラウドは GitHub push 済みのみ）が異なるため、誤操作リスクあり。暫定回避策: クラウド環境名を `(cloud)` と手動付記する。Feature Request として「Local · \<Mac 名\>」vs「Cloud · \<環境名\>」のバッジ表示を求めている。

- **[Issue #97909（2026-09-28 作成 / OPEN）— モバイルアプリがスキル実行時の引数をメッセージバブルに表示しない](https://github.com/anthropics/claude-code/issues/97909)** — CLI から `/wayfinder 1096` のように引数付きスキルを実行したとき、iOS モバイルアプリ側のセッション追跡画面には `/wayfinder` のみ表示され引数 `1096` が欠落する。CLI から実行中のスキルがどんな入力を受け取ったかが iOS 側から判別できないという問題。v2.1.283 で確認。

- **[Issue #97744（2026-09-28 作成 / OPEN）— モバイルアプリと Web でセッション/グループの表示が不一致](https://github.com/anthropics/claude-code/issues/97744)** — デスクトップアプリでは 6 セッションすべてがカスタムグループに正しく表示されるのに、iOS ネイティブアプリでは 4 セッションのみ表示（最近アクティブだったセッションが欠落するケースも）、Web（claude.ai/code）では大半が「未グループ」に移動して表示される。データ自体は存在しており、表示・同期ロジックの問題と推定。

- **[Issue #96867（2026-09-24 作成 / OPEN）— モバイルアプリから Claude Desktop の新規コードセッションを開始できるようにしてほしい](https://github.com/anthropics/claude-code/issues/96867)** — 現状、iOS アプリの Code タブから「デスクトップ PC のペアリング済みフォルダを指定して新規ローカルセッション」を開始する手段がない（既存セッションの再開のみ可能）。外出先で思いついたタスクを自宅 PC の Claude Desktop で開始し、帰宅後に引き継ぐというワークフローを Feature Request として提案。

- **[Issue #96941（2026-09-25 作成 / OPEN）— iOS シミュレーターパネルが iPhone Duo の折りたたみ状態・向きを無視する](https://github.com/anthropics/claude-code/issues/96941)** — Claude Desktop の iOS Simulator パネルが、Xcode 27.1 beta の Device Hub で展開・横向きにした iPhone Duo（折りたたみスマートフォン）の状態を追従せず、パネルの表示が 90° 回転したまま・座標空間も折りたたみ時のまま（466×678pt）・screenshot は `captureFailed` で失敗。暫定回避策: Device Hub で操作し、スクリーンショットは `xcrun simctl io <udid> screenshot` を使う。

## 📝 日本語コミュニティ

- 該当なし（2026-09-29 時点で Zenn・Qiita の新規 Claude Code × iOS / Swift / Xcode 記事は確認できず）

## 🌐 海外コミュニティ / Tips

- **[Claude Code v2.1.284 リリースサマリ — Havoptic](https://www.havoptic.com/tools/claude-code)** — v2.1.284 の変更点を一覧形式でまとめたサードパーティのリリースサマリサイト。iOS 開発への直接的な変更はないが、Sonnet 5.5 へのデフォルト切り替えはコード生成・コードレビューの精度向上につながるため Swift/SwiftUI プロジェクトでのコーディング品質改善が期待できる。

## 💡 今日のおすすめ実践 Tip

**「クラウドセッションとローカルセッションを名前で区別して誤操作を防ぐ（Issue #97734 暫定対策）」**

Issue #97734 が修正されるまでの間、iOS/iPad アプリ上でどちらのセッションが動いているかを一目で判断するために、**セッション名またはリポジトリ名にプレフィックスを付ける**方法が効果的です。

```markdown
# CLAUDE.md に以下を追記（クラウドセッション用）
# このプロジェクト設定はクラウドセッション専用。
# セッション開始時に必ずセッション名を "☁ <リポ名>" の形式で設定する。
```

あるいは Claude Code の設定でクラウド環境名を固定する方法:

```bash
# claude.ai/code のプロジェクト設定から環境名を設定
# 例: "☁ MyApp (cloud)" と命名することで iOS タブ上での視認性を確保
```

**セッション種別の判断チェックリスト**（iOS アプリで確認できる現状の手がかり）:

| 判断材料 | クラウドセッション | ローカル（Remote Control）セッション |
|---|---|---|
| タイトルバー | `<repo> · <環境名>` | `<repo>` のみ |
| ファイルアクセス | GitHub push 済みのみ | Mac のローカルファイル全般 |
| クォータ消費 | クラウドクレジット | 週 5 時間プランタイム |
| オフライン時 | 継続動作 | Mac がオンラインのみ |

このチェックリストを CLAUDE.md に貼っておくと、iOS からセッションを開いた際にすぐ確認できて便利です。
