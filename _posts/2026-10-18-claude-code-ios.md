---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-10-18"
date: 2026-10-18 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- 該当なし（2026-10-18 時点で直近の v2.1.300 は 10-15 キャッチアップ済み。v2.1.301 以降のリリースは未確認）

## 🛠 GitHub の動き

- **[Xcode 27 / DeviceHub 対応の iOS Simulator 関連 Issue 5 件が一斉クローズ（2026-10-15〜10-17）](https://github.com/anthropics/claude-code/issues/95466)** — Xcode 27 で Apple が `Simulator.app` を廃止して `DeviceHub`（ヘッドレス常駐デーモン）に刷新したことで生じた一連の iOS Simulator 障害が、複数の Issue として報告されていた（[#95466](https://github.com/anthropics/claude-code/issues/95466)・[#79991](https://github.com/anthropics/claude-code/issues/79991)・[#97826](https://github.com/anthropics/claude-code/issues/97826)・[#80425](https://github.com/anthropics/claude-code/issues/80425)・[#80180](https://github.com/anthropics/claude-code/issues/80180)）。主な症状は「touch/tap インジェクションがサイレント no-op になる」「`No booted simulator found` エラーが出続ける」「SimulatorKit.framework の移動により内部エラーが握りつぶされる」の 3 パターン。これらが 10-15〜10-17 にかけてまとめてクローズされた。Xcode 27 GA 環境での Claude Code iOS Simulator 機能を使っていた開発者は最新版への更新で改善が見込まれる。

## 📝 日本語コミュニティ

- **[AI に iOS アプリを実装させるとき、触らせてはいけないファイルと守り方（qiita.com/yasu-ya）](https://qiita.com/yasu-ya/items/c5b28fe4781d96723b20)** — Xcode 26.6 + GitHub Actions + Claude Code の環境で検証。AI に変更させると壊れやすいファイルを 6 グループ（CI パイプライン設定・コード署名・依存マニフェスト・ビルド設定・機密ファイル・自動生成ファイル）に分類し、それぞれの守り方（CLAUDE.md への禁止指示・PreToolUse フックによるガード・`.gitignore`/`.claudeignore` の活用）を整理。`xcconfig`・`project.pbxproj`・`GoogleService-Info.plist` 等が代表例として挙げられている。

- **[AI に iOS アプリを実装させるとき、失敗を種類で分ける（qiita.com/yasu-ya）](https://qiita.com/yasu-ya/items/989a098d54e9979b7588)** — Claude Code が GitHub Issue から iOS アプリを実装し、ビルド・テスト・E2E を経て PR を開くパイプラインを構築した事例。失敗を「依頼内容の不足」「作業範囲の大きすぎ」「環境の問題」「禁止ファイルの変更」の 4 種に分類し、種類ごとにリトライ戦略を変えることが重要だと指摘。実装失敗だけをエージェントに差し戻しても無意味で、種類を判定して人間にエスカレーションすべきケースを明確にする設計が実務での安定運用のカギ。

## 🌐 海外コミュニティ / Tips

- **[Apple Support アプリ v5.13 に CLAUDE.md が混入 — 24 時間以内に v5.13.1 で修正（feedbagel.com 他）](https://feedbagel.com/post/apple-accidentally-bundles-claude-ai-files-in-apple-support-app-update-v513)** — 2026 年 4 月、Apple の公式サポートアプリ v5.13 のバンドル内に Claude Code 向け指示ファイル `CLAUDE.md`（2 ファイル）が混入しているのを開発者が発見。中身は機密情報ではなくコーディング規約の指示だったが、本来は配布物に含めるべきでないファイルであり、Apple は約 24 時間後に v5.13.1 を緊急リリースして削除した（同様の事例は 2026 年 6 月の Netflix iOS アプリでも報告）。iOS アプリ開発者にとっての教訓は「ビルドパイプラインで AI コンテキストファイルをアプリバンドルから確実に除外する仕組みを作る」こと（下の Tip 参照）。

## 💡 今日のおすすめ実践 Tip

**CLAUDE.md / `.mcp.json` をリリースビルドに混入させない 3 つの防衛線**

Apple や Netflix の事例を教訓に、AI コンテキストファイルがアプリバンドルに紛れ込まないよう、以下の 3 つの防衛線を組み合わせるとよい。

**① `.claudeignore` でコピー元から除外**

プロジェクトルートに `.claudeignore` を置き、AI ツールが生成・参照するファイルを Claude Code の操作対象から明示的に除外する（`Read` ツールに渡らないようになる）：

```
# .claudeignore
CLAUDE.md
.mcp.json
.claude/
*.xcconfig
project.pbxproj
```

**② Xcode の "Copy Bundle Resources" フェーズから除外**

`CLAUDE.md` や `.mcp.json` が Xcode プロジェクトに誤って追加されてしまっていないか、`Copy Bundle Resources` ビルドフェーズを確認する。含まれていた場合はフェーズから削除し、ターゲットのメンバーシップも外す。

```bash
# 現在のバンドル内に AI コンテキストファイルが入っていないか確認
find "$(xcodebuild -showBuildSettings 2>/dev/null | grep " BUILT_PRODUCTS_DIR" | awk '{print $3}')" \
  -name "CLAUDE.md" -o -name ".mcp.json" 2>/dev/null
```

**③ CI の最終チェックステップに検査を追加**

GitHub Actions のアーカイブ後・アップロード前に以下のステップを追加し、混入を検知したらビルドを失敗させる：

```yaml
- name: Check for AI context files in app bundle
  run: |
    APP_PATH=$(find ~/Library/Developer/Xcode/Archives -name "*.app" | head -1)
    if find "$APP_PATH" -name "CLAUDE.md" -o -name ".mcp.json" | grep -q .; then
      echo "ERROR: AI context files found in app bundle!" >&2
      exit 1
    fi
```

これら 3 つを組み合わせることで、開発環境では Claude Code が `CLAUDE.md` を正常に参照しながら、リリースビルドには一切含まれない構成になる。
