---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-11-12"
date: 2026-11-12 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Claude Fable 5.2 / Mythos 5.2 リリース（2026-11-09）](https://www.anthropic.com/news/claude-fable-and-mythos-5-2) — コーディング・推論・ビジョンが強化された新モデル。中程度のタスクを Fable 5.1 より少ないトークンで完了できるよう改善されており、iOS 開発では Swift 6 コード生成精度と API の最新状況への追従が向上している。同日リリースの **v2.1.321** で Claude Code のデフォルトモデルが Fable 5.2 に切り替わった。v2.1.321 ではこのほか Remote Control への設定ファイルサポート追加、自動モードでの Claude 製モッドを確認なしで有効化する変更、パーミッション周りの 4 件の修正が含まれる。
- [v2.1.322（2026-11-11）](https://releasebot.io/updates/anthropic) — Vertex AI の global / US / EU リージョンで fast mode が利用可能に（Vertex 側に拒否された場合は標準速度にフォールバック）。cloud session でのプロセス終了コマンドの不具合修正、ネストされたスキル読み込み処理の改善も含む。
- **Opus 4.8 fast mode 廃止予告** — 2026-11-30 に削除予定。削除後は fast mode リクエストがエラーを返し、標準モードにフォールバックしない。iOS CI で `--model claude-opus-4-8 --fast` を明示している場合は **11-30 まで** に設定を見直す必要がある（`claude-fable-5-2` や `claude-sonnet-5-5` への切り替えを推奨）。

## 🛠 GitHub の動き

- [rshankras/claude-code-apple-skills（GitHub）](https://github.com/rshankras/claude-code-apple-skills) — iOS / macOS / SwiftUI 向け Claude Code スキルコレクション。iOS 開発・Apple Intelligence 統合・設計・App Store 対応・アーキテクチャ仕様など複数カテゴリのスキルを含み、WWDC コンテンツ境界ごとにタグ（`wwdc25-era-final` など）を切ってスナップショットを保持している。インストールは `npx skills add rshankras/claude-code-apple-skills` または Claude Code プラグイン経由。最新 Xcode + Apple Silicon 環境でのプロジェクト初期化スキルが特に充実している。

## 📝 日本語コミュニティ

- [【Swift未経験】Claude CodeでiPhoneアプリを作って審査に出すまで（Qiita / masa1124）](https://qiita.com/masa1124/items/6b66b5f27158e7067ff9) — Swift 経験ゼロの著者が Claude Code のみを頼りに SwiftUI + SwiftData + StoreKit 2 を使ったアプリを App Store 審査まで提出した実践レポート。CLAUDE.md に仕様を先に書いてから実装を進める手順が有効だったとされる一方、Xcode の署名設定や App Store Connect のメタデータ入力は手動作業が残ると整理されており、AI 活用の実態感がある内容。

## 🌐 海外コミュニティ / Tips

- [Fatbobman's Swift Weekly #122 — Xcode Enters the Agent Era（fatbobman.com）](https://fatbobman.com/en/weekly/issue-122/) — 「Xcode がエージェント時代に突入した」という視点で Claude Code を軸に特集した号。Xcode 26.3 が Claude Code / Codex を直接サポートしたことを踏まえ、MCP・スキル・適応的設定（Adaptive Configuration）・モデル切り替えを組み合わせた実践的なセットアップ手順を解説。同名の詳細記事 [Xcode 26.3 + Claude Agent: Model Swapping, MCP, Skills, and Adaptive Configuration](https://fatbobman.com/en/posts/xcode-263-claude/) も参照するとセット感がある。

## 💡 今日のおすすめ実践 Tip

**Opus 4.8 fast mode 廃止（2026-11-30）に備えた iOS CI 設定の移行**

GitHub Actions や Xcode Cloud など iOS CI で Claude Code を使っている場合、`--fast` フラグと `claude-opus-4-8` の組み合わせを明示していると 11-30 以降にエラーが発生する。今週中に `.github/workflows/*.yml` や CI 設定を確認し、以下の方針で移行しておくとよい：

```yaml
# Before（2026-11-30 以降エラー）
- run: claude --model claude-opus-4-8 --fast "$PROMPT"

# After（Fable 5.2 に切り替え）
- run: claude --model claude-fable-5-2 "$PROMPT"
```

Fable 5.2 はデフォルトモデルになったため、モデル指定を省略しても自動的に適用される。iOS ビルド・テストのプロンプトを渡す用途では Fable 5.2 の方がコーディング精度が上がっている分、`--fast` を使っていた目的（高速化）とのバランスを確認するとよい。速度を重視する場合は Fable 5.2 標準モードか Haiku 5.5 を検討し、ヘッドレス CI 向けには `permissions.denyInsteadOfPrompting` と組み合わせた設定が引き続き有効。
