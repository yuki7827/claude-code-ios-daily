---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-11-21"
date: 2026-11-21 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [v2.1.328（2026-11-18〜19 ごろ）](https://code.claude.com/docs/en/changelog) — 11/17 の v2.1.327 に続く継続リリース。詳細は公式 Changelog を参照。iOS チームへの影響として注目は **Opus 4.8 Fast Mode の廃止期限（2026-11-30）**。10/30 に Deprecated になった Opus 4.8 の Fast Mode が月末（11/30）に完全削除される予定のため、CLAUDE.md や CI 設定で `model: claude-opus-4-8` かつ `fastMode: true` を指定しているワークフローは **今週中に設定変更が必要**。推奨移行先は Claude Opus 5.5（`claude-opus-5-5`）または Claude Sonnet 5.5（`claude-sonnet-5-5`）。

## 🛠 GitHub の動き

- [getsentry/MobileBuildMCP v2.7.1（旧 XcodeBuildMCP が改名）](https://github.com/getsentry/MobileBuildMCP) — **XcodeBuildMCP が MobileBuildMCP に改名**され、v2.7.1 がリリースされた。npm パッケージ名・CLI バイナリ名・環境変数・設定ディレクトリがすべて新名称に変更。**v2.7.0 は schema version 3 への移行を含む破壊的変更**で、旧 version 2 のバリデーターをピンしている MCP クライアント側は新しいビルド／テスト結果を拒否する。また `env`・`testRunnerEnv` のような辞書形式入力がキー・バリューの配列形式に変更。Xcode 27 の Device Hub シミュレーターへの UI 操作対応（タップ・スワイプ・キーボード）も v2.7.0 で入っており、旧 Simulator.app にはフォールバック。`xcodebuildmcp.com` ドメインは廃止済み。

## 📝 日本語コミュニティ

- [iPhoneから Claude Code を操作できるアプリ「NeriTerm」をリリースした（Qiita / nerikeshi）](https://qiita.com/nerikeshi/items/6e50dc84a390feedbc1c) — iOS の Swift で書かれた SSH クライアント兼ターミナルアプリ「NeriTerm」の作者自身によるリリース記録。Claude Code の操作に特化したカスタム UI（ファンクションキーバー・プリセットコマンド送信・出力のコピー）を持ち、App Store に申請中とのこと。VibeTerm・TermPod・SSH Term X など先行アプリとの差分として「iOS 標準フォントで日本語入力を崩さない」点を挙げている。

- [Claude Code のサブエージェント機能で iOS / Flutter 向けコードレビュー職人を作った話（Qiita / kotaro_ai_lab）](https://qiita.com/kotaro_ai_lab/items/e572c539bd282869404e) — `.claude/agents/` に `review-swift.md`・`review-flutter.md` を配置し、コードレビューを専担するサブエージェントを構築した実践記録。Swift エージェントは `@MainActor` 分離・SwiftUI ライフサイクル・TCA アーキテクチャ整合性を担当し、Flutter エージェントはウィジェットベストプラクティス・状態管理・Dart イディオムを担当。PR コメントの自動投稿まで連携させており、同一会話で実装とレビューを兼任させると Claude が自分のコードを過大評価するバイアスがかかる問題を別エージェントで解消した例として参考になる。

- [Claude Code でゼロからアプリを審査まで！OshiLog が教えてくれた全自動リリースフローの裏側（Qiita / sorabcjanne1）](https://qiita.com/sorabcjanne1/items/ec82858881ea3b246f59) — 推し活ログアプリ「OshiLog」（iOS: Swift + Firebase）を Claude Code で 0 から実装し App Store 申請まで通した実践記録（2026-11-19 投稿）。企画 → PRD → CLAUDE.md 生成 → 実装 → 自動テスト → TestFlight → 審査まですべて Claude Code + Fastlane + ASC API で自動化したパイプラインを公開。審査で初回リジェクト（プライバシーポリシー不備）があり、Claude Code に修正を依頼して再申請した際の差分も掲載。

## 🌐 海外コミュニティ / Tips

- [This week in Claude Code, Codex and Gemini CLI — Week of Nov 15, 2026（DEV Community / aicoding-guide）](https://dev.to/aicoding-guide/this-week-in-claude-code-codex-and-gemini-cli-week-of-november-15-2026-4pmm) — 11/15 週の Claude Code・Codex・Gemini CLI の動向を週次まとめ。iOS 文脈では「MobileBuildMCP への改名と schema v3 の破壊的変更」「`claude mcp add-json ... -` を使ったシークレット安全注入（v2.1.327）」「Duo シミュレーターの任意角度折りたたみ（Claude Code Desktop）」の 3 点がハイライト。

- [Claude Code for iOS App Development: The Workflow, Not the Setup（DEV Community / 5uper0）](https://dev.to/5uper0/claude-code-for-ios-app-development-the-workflow-not-the-setup-5bfo) — 多くのセットアップガイドが「XcodeBuildMCP を入れて終わり」になりがちな点を批判し、**セットアップ後のワークフロー**にフォーカスした記事。「Swift Package Manager モジュールで新規コードを管理し `.xcodeproj` ファイルを Claude に直接触らせない」「ビルドエラーは全文ではなくエラー行前後 20 行だけ渡す」「テストは仕様として先に書かせ、実装後に回す」という 3 原則をワークフロー例付きで解説。MobileBuildMCP の rename 後の設定例（`npx mobilebuildmcp mcp`）も記載されており、既存セットアップからの移行リファレンスとしても使える。

## 💡 今日のおすすめ実践 Tip

**XcodeBuildMCP → MobileBuildMCP 移行チェックリスト（今月末までに対応推奨）**

v2.7.1 での rename は破壊的変更を含むため、既存セットアップのある iOS チームは下記を確認する。

```bash
# 1. 現在の登録名を確認
claude mcp list

# 2. 旧名で登録されている場合は削除して再登録
claude mcp remove xcodebuild          # 旧名
npx mobilebuildmcp@latest setup       # 新名で自動設定（推奨）

# または手動で再登録
claude mcp add --transport stdio mobilebuild -- npx -y mobilebuildmcp mcp

# 3. 環境変数名も変わっているため .env / CI secrets を更新
# 旧: XCODEBUILDMCP_* → 新: MOBILEBUILDMCP_*
```

加えて、CI の `claude mcp add-json` で schema バリデーターをピンしている場合は version 2 → 3 への更新が必要。旧 version 2 のバリデーターは新形式の structured result を拒否してビルドチェックがサイレントに失敗するため、特に GitHub Actions などの無人 CI 環境は今週中に動作確認を行うこと。
