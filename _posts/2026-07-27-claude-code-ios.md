---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-07-27"
date: 2026-07-27 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

該当なし（直近の新リリースは v2.1.220（2026-07-25）で、[前回の記事](https://yuki7827.github.io/claude-code-ios-daily/)で取り上げ済み。本日時点で v2.1.221 以降の公式リリースは確認されていない）

## 🛠 GitHub の動き

- [Issue #81466 — Desktop-hosted セッションが終了と同時に自動アーカイブされ、iOS の Code リストから消える（OPEN / 2026-07-19〜）](https://github.com/anthropics/claude-code/issues/81466) — 2026-07-19 のデスクトップアプリ更新以降、Mac 上で起動したすべての Claude Code セッションが終了時点で自動的にアーカイブされる問題。iOS / Web の Code リストは `status: active` のセッションしか表示しないため、Mac 側で完結したセッションが iPhone から即座に不可視になる。`claude remote-control` デーモン経由のセッションは影響を受けない。**回避策**：非公開だが有効なエンドポイント `POST /v1/code/sessions/<session_id>/unarchive` を叩くと 200 が返り、アーカイブを解除できる（ローカルの `local_<id>.json` の `isArchived` 書き換えは Desktop 側にしか効かないため注意）。`/var/folders` クリア後に再発する場合は再実行が必要。iOS からセッション履歴にアクセスする「Mac-as-host」ワークフローを採用しているチームは要注意。Anthropic 側での修正を待ちながら、デーモン経由（`claude remote-control`）に切り替えるのが安定した対処法。

- [Issue #81434 — Remote Control の無効化トグルを操作すると「Cannot read properties of undefined (reading 'session_url')」エラーが常に発生（OPEN / 2026-07-26）](https://github.com/anthropics/claude-code/issues/81434) — Claude Desktop アプリの「Settings → Claude Code → Enable remote control by default」トグルを **OFF** にすると、毎回 TypeError がスローされてトグルが元に戻り、Remote Control を UI 経由で無効化できなくなるバグ。有効化（ON）は正常に動作するが、無効化パスのみクラッシュする。副作用として iOS アプリ側でもセッション状態の同期不整合が発生し、①セッション名がプロセス名に化ける、②iOS 側でアーカイブ済みと表示されるのに Desktop では `isArchived: false` になる、③iOS 側から unarchive すると両方のリストから消える、という 3 つの症状が報告されている。同種エラーは Issue #78933・#78482 でも発生しており、`session_url` を参照しているオブジェクトの定義もれが根本原因と見られる。当面の回避策は、アプリを強制終了するかセッションを停止して再起動すること。

## 📝 日本語コミュニティ

- [Claude / Anthropic 関連ニュースまとめ（2026-07-27）（Qiita / homhom44）](https://qiita.com/homhom44/items/faaa3d738bbffa2b62c9) — 2026-07-27 分の Claude / Anthropic 関連ニュースをコンパクトにまとめた記事。毎日更新されており、iOS 関連 Issue や Claude Code の最新動向を手早く把握したい場合に便利。

## 🌐 海外コミュニティ / Tips

- [10 Tips to Maximize Claude Code for iOS Development（Jerry PM / Medium / CodeToDeploy / 2026-07）](https://medium.com/codetodeploy/10-tips-to-maximize-claude-code-for-ios-development-29027edc51a7) — Claude Code を iOS 開発で最大限活用するための実践 Tips 10 選。「Claude Code を Apple SDK を全暗記したシニアエンジニアとして扱え（指示を出す相手として使うのではなく、プラットフォームへの深い理解を持つパートナーとして活用せよ）」という考え方を軸に、XcodeBuildMCP との組み合わせや CLAUDE.md の書き方、SwiftUI 固有のコーディング指示など、実務で即使えるテクニックを紹介している。

## 💡 今日のおすすめ実践 Tip

**「デスクトップセッション自動アーカイブ問題の対処：`remote-control` デーモンへの移行と unarchive エンドポイントの活用」**

Issue #81466 の影響を受けている場合（Mac 上で完結した Claude Code セッションが iPhone から見えなくなっている場合）、以下 2 つの対策を組み合わせると最も安定します。

**対策①：既存のアーカイブ済みセッションを一括復元する**

セッション ID 一覧を取得してからエンドポイントを叩きます（`CLAUDE_API_KEY` は `.env` 等で管理してください）：

```bash
# アーカイブ済みセッション ID を取得
ARCHIVED=$(curl -s -H "Authorization: Bearer $CLAUDE_API_KEY" \
  "https://api.claude.ai/v1/code/sessions?status=archived&limit=50" \
  | jq -r '.sessions[].id')

# 一括 unarchive
for sid in $ARCHIVED; do
  curl -s -X POST -H "Authorization: Bearer $CLAUDE_API_KEY" \
    "https://api.claude.ai/v1/code/sessions/$sid/unarchive"
  echo "Unarchived: $sid"
done
```

**対策②：今後は `claude remote-control` デーモン経由のセッションに切り替える**

Issue #81466 では `config.origin: claude_code_cli` かつ `environment_id` が空のセッション（= Mac デスクトップから直接起動したセッション）のみが自動アーカイブされます。`claude remote-control` 経由のセッションは `environment_id` が設定されるため影響を受けません。

前日の Tips（Issue #80827 の launchd 設定）と組み合わせて Remote Control デーモンを常時起動しておくと、iPhone から見えるセッションの安定性が大幅に向上します：

```bash
# Remote Control デーモンを手動起動（デーモン化は前日 Tip の launchd 設定参照）
claude remote-control \
  --permission-mode bypassPermissions \
  --name "MyMac" &

# iPhone から Dispatch でこのデーモン経由のセッションを開く
# → 終了後も自動アーカイブされず Code リストに残る
```
