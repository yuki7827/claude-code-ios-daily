---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-06-29"
date: 2026-06-29 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- 本日時点（2026-06-29 JST）の最新リリースは [v2.1.195（2026-06-26）](https://github.com/anthropics/claude-code/releases/tag/v2.1.195) のまま変更なし。詳細は [06-28 の記事](https://yuki7827.github.io/claude-code-ios-daily/)を参照。

## 🛠 GitHub の動き

- [artemnovichkov/iOS-26-by-Examples](https://github.com/artemnovichkov/iOS-26-by-Examples) — Artem Novichkov による iOS 26 新機能を SwiftUI で実演するサンプル集（98 stars）。GlassEffect・GlassEffectContainer・`@Animatable` マクロ・Chart 3D・RichTextEditor・NativeWebView・Foundation Models `LanguageModelSession` など 12 機能をそれぞれ自己完結した example で収録。プロジェクトルートに **CLAUDE.md** が配置されており、Claude Code が「Xcode 26.0 beta 4+・iOS 26.0+ が必要」「`LanguageModelSession` の custom tools で GitHub 情報と星座を組み合わせた Developer Horoscope を実装している」などのプロジェクト文脈を即理解できる構成になっている。iOS 26 API の正しい使い方を Claude Code に参照させる実例リポとして活用できる。

- [twostraws/SwiftAgents](https://github.com/twostraws/SwiftAgents) — Hacking with Swift の Paul Hudson による **AGENTS.md** ファイル（1.4k stars・86 forks）。LLM が生成する Swift コードに deprecated API やレガシーパターンが混入するのを防ぐため、「iOS 26 以降の最新 API を優先するよう AI に強く指示する」内容を汎用フォーマットで記述したもの。Claude Code・GitHub Copilot など幅広いツールに対応し、プロジェクトルートに置くだけで効果を発揮する。同作者の `SwiftUI-Agent-Skill`・`Swift-Concurrency-Agent-Skill` などと異なり、SKILL.md ではなく **AGENTS.md 形式**で提供される点が特徴。

## 📝 日本語コミュニティ

- [Claude Code 週次アップデートまとめ（2026/06/27週）（Qiita / saitoko）](https://qiita.com/saitoko/items/f96b5858170b3a559382) — v2.1.185〜v2.1.195（2026/06/20〜06/26）の変更点を日本語で整理した週次まとめ。iOS CI 環境に影響する v2.1.195 のフックマッチャー修正（ハイフン入り識別子が exact match に変更）・v2.1.193 の `autoMode.classifyAllShell` 追加・v2.1.191 の `/rewind` 拡張と CPU 37% 削減など要点を効率よく把握できる。設定変更が必要なケースも明示されており、週次の変更追従チェックリストとして活用しやすい。

- [【第2弾】VS Code の Claude Code で「iOS コードレビュー → 修正 → Xcode ビルド確認 → テスト項目作成」を自動化する（Qiita / 4q_sano）](https://qiita.com/4q_sano/items/effe51de5e9654777f1e) — 06-28 で紹介した第 1 弾（コードレビュー→修正→ビルド確認）の続編。今回は **テスト項目書の自動生成**まで拡張する手順を解説。VS Code 拡張 Claude Code から xcodebuild 結果を受け取り、修正後のコードに対するテストケースを Claude が起こす一連のフローを初心者向けに順を追って説明。

- [【コピペで使える】Claude Code のカスタムエージェント 7 選 — iOS 個人アプリ開発者が今すぐ作るべき .md ファイル全公開（Qiita / kotaro_ai_lab）](https://qiita.com/kotaro_ai_lab/items/b6bb6e3c67f66eeede6b) — `.claude/agents/` に置く Markdown ファイルでサブエージェントを定義し、繰り返し作業を一定品質で自動化する手法を解説。iOS 個人開発向けに「コードレビュー・リファクタリング・テスト・App Store メタデータ生成・バグ修正・ドキュメント作成・Localizable.strings 翻訳」7 エージェントの `.md` を全公開しており、コピペで即導入できる。

## 🌐 海外コミュニティ / Tips

- [iOS Project Claude Code Setup Prompt（gist / joelklabo）](https://gist.github.com/joelklabo/6df9fa603bec3478dec7efc17ea44596) — XcodeBuildMCP を前提にした iOS プロジェクト向け Claude Code 完全セットアップ Gist。① 環境検証 → ② プロジェクト分析 → ③ CLAUDE.md 作成 → ④ XcodeBuildMCP 設定 → ⑤ `/build`・`/test`・`/run` などカスタムスラッシュコマンド → ⑥ フック自動化（セッション起動時・Swift ファイル保存時）→ ⑦ ドキュメント階層構築 → ⑧ 確認テスト の 8 フェーズで構成。プロジェクト構造を自動検出して適切なビルドコマンドを選択するコンテキスト検出システムが特徴で、他のガイドより **XcodeBuildMCP 中心の構成** を徹底している。

## 💡 今日のおすすめ実践 Tip

**「`twostraws/SwiftAgents` の AGENTS.md を CLAUDE.md と組み合わせて iOS 26 API 優先を徹底する」**

`twostraws/SwiftAgents` の AGENTS.md をプロジェクトルートの CLAUDE.md と一緒に置くことで、Claude Code に「iOS 26 以降の最新 API のみを使う」という制約を明示的に伝えられます。

```
# プロジェクトルートに両方を配置
.
├── AGENTS.md      # twostraws/SwiftAgents からコピー（iOS 26+ API 優先ルール）
├── CLAUDE.md      # プロジェクト固有の設定（スキーム・MCP・ビルドコマンド）
└── MyApp.xcodeproj
```

AGENTS.md の主な効果：

- deprecated API（`UIAlertView` 等）や UIKit レガシーパターンを Claude が自動的に避ける
- Liquid Glass（`GlassEffect`）・`@Observable`・Foundation Models（`LanguageModelSession`）など iOS 26 固有 API を積極採用するようになる

さらに `artemnovichkov/iOS-26-by-Examples` の実装例を参照として Claude Code に渡すと、iOS 26 API の正確な使い方をセッション開始直後から理解した状態で作業を開始できます。

```
# Claude Code セッション例
「AGENTS.md を参照のうえ、artemnovichkov/iOS-26-by-Examples の
 GlassEffectView.swift を参考にしてログイン画面に Liquid Glass を実装して」
```

CLAUDE.md でプロジェクト固有のスキームや XcodeBuildMCP 設定を伝え、AGENTS.md で「どの API を使うか」の指針を持たせることで、iOS 26 対応アプリを Claude Code でゼロから作る際のハルシネーション（旧 API 混入）を構造的に防げます。
