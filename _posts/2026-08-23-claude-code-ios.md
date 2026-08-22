---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-08-23"
date: 2026-08-23 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- **v2.1.239（2026-08-21）リリース**（[Claude Code 変更ログ](https://code.claude.com/docs/en/changelog)）— iOS 開発観点での主な変更点: ① **データレジデンシーワークスペースのコスト表示が正確に** — AWS や GCP 上で Data Residency ワークスペースを使っているエンタープライズ iOS チーム向けに、コスト見積もりに 1.1× US-only-inference プレミアムが加算されて表示されるようになった。課金の透明性が向上する; ② **Bedrock / Vertex / Foundry でフルスクリーンレンダラーを提供開始** — これまでカスタムベース URL を使う環境ではフルスクリーンモードが利用できなかったが、今回の修正で CI/CD パイプライン上の Claude Code セッションでもフルスクリーン UI が使えるように; ③ **クラウドセッション: claude.ai から同期したプラグインが `name@synced` 形式で表示** — チームで claude.ai からプラグインを共有している場合、セッション上での識別が明確になった。XcodeBuildMCP などのプラグインをチーム共有する際に状態を把握しやすくなる; ④ **Alpine / musl ビルド環境で画像ペースト・クリップボード・音声キャプチャアドオンが正常に動作** — Linux ベースの CI ランナー（Alpine コンテナ）で Claude Code を使うケースで、これらのアドオンが機能しなかった問題が解消; ⑤ **`/claude-api upgrade` スキル追加** — Python の anthropic 0.x から 1.x へのマイグレーションを自動化するスキル。iOS バックエンドを Python で実装しているチームで SDK 更新を Claude に任せられる; ⑥ **その他バグ修正**: Vim モードのバグ修正・セッションタイトル表示の修正・OpenTelemetry トレースの断片化修正・`/insights` が `<message>` タグをそのまま出力する問題の修正・ワークフロー詳細ダイアログのオーバーフロー修正・ブラウザターミナルでマウス移動時に文字化けが入る問題の修正

- **v2.1.240（2026-08-22）リリース**（[Claude Code 変更ログ](https://code.claude.com/docs/en/changelog)）— バグ修正と信頼性の改善のみ。新機能なし。

## 🛠 GitHub の動き

- 該当なし（2026-08-22〜23 の範囲で iOS / Swift / Xcode / Simulator に特化した新規 Issue・PR は確認できず）

## 📝 日本語コミュニティ

- 該当なし（2026-08-22〜23 の範囲で iOS / Swift / Xcode に特化した新規記事は確認できず）

## 🌐 海外コミュニティ / Tips

- [xclaude-plugin（conorluddy / GitHub）](https://github.com/conorluddy/xclaude-plugin) — iOS 開発特化の Claude Code プラグイン。8 つのワークフロー別 MCP サーバー・24 ツールで構成され、「必要な MCP だけ有効化してコンテキストウィンドウを軽量に保つ」設計が特徴。最大の差別化点は **Xcode の出力（ビルドエラー・テスト結果・ビルドログ）を構造化 JSON に変換して Claude に渡す**ことで、生のコマンド出力を LLM に食わせる場合と比べてトークン消費を最大 87% 削減できるとされる。UI オートメーションはスクリーンショット（2000ms）ではなくアクセシビリティツリーのクエリ（120ms）で行うため、ビルド→テスト→UI 確認のサイクルが高速。同作者の `ios-simulator-skill`（シミュレータ操作専用）より広範な用途をカバーする上位互換にあたる。全投稿での初掲載。インストール: `/plugin marketplace add conorluddy/xclaude-plugin`

- [apple-skills（Prisma-Labs-Dev / GitHub）](https://github.com/vabole/apple-skills) — Claude Code・Codex などのコーディングエージェント向けに iOS 26+ の最新 API リファレンスをスキルとして提供するリポジトリ。SwiftUI・UIKit・Swift Testing・Swift Concurrency・SwiftData・HealthKit・Combine・StoreKit 2・MapKit・TipKit・**Liquid Glass デザインシステム**をカバーする。「LLM の学習データには古い API の情報がすでに含まれているため、スキルは最新 API に絞る」という設計思想が明確。`@Observable` マクロ・`NavigationStack`・async/await・Swift 6 規約を前提とした現代的な記述で、アーキテクチャに関する「強い意見」（`ios-ui-craft` 等）はあえて無効化し、エージェントが独自の設計判断を下せるよう余地を残した構成になっている。全投稿での初掲載。

- [The Complete Guide to Building an iOS App with Claude Code (No Xcode Required)（dev.to / Mary Piakovski）](https://dev.to/marypiakovski/the-complete-guide-to-building-an-ios-app-with-claude-code-no-xcode-required-o5b) — TypeScript / React Native で iOS アプリを実装し、ビルドは Expo EAS Build（クラウド上）で行うことで **ローカルに Xcode を一切インストールせず** Claude Code だけで iOS アプリを完成させるアプローチの解説記事。XcodeBuildMCP が前提とする「ローカルに Xcode が必要」という制約を回避する方法として、Xcode なし環境（Linux CI・Chromebook・Windows 等）でも iOS 開発に Claude Code を活用したいユーザーへの選択肢になる。「Swift ネイティブにこだわらない・まずアイデアを素早く形にしたい」用途向きのアプローチ。全投稿での初掲載。

## 💡 今日のおすすめ実践 Tip

**「xclaude-plugin でビルドフィードバックループを高速化 — 生の Xcode 出力をトークン 87% 削減の構造化 JSON に変換する」**

Claude Code から直接 `xcodebuild` を呼び出すと、1 回のビルドで 50〜300 行ものログが流れ込み、LLM が処理するトークン量がかさみます。xclaude-plugin はこの問題をエラーの構造化 JSON 変換で解決しています。

**インストール（2 コマンド）**

```bash
# Claude Code 内で実行
/plugin marketplace add conorluddy/xclaude-plugin
/plugin install xclaude-plugin
```

**ワークフロー別に MCP を選択する（必要最小限を有効化）**

| MCP 名 | 用途 |
|---|---|
| `xcode-build` | ビルド検証・エラー抽出 |
| `xcode-simulator` | シミュレータのライフサイクル管理 |
| `xcode-ui-automation` | アクセシビリティツリーによる UI 操作 |
| `xcode-test` | テスト実行・結果パース |

ビルドのみ確認したいセッションでは `xcode-build` だけを有効化し、UI 自動化が必要なセッションで `xcode-ui-automation` を追加するという使い方で、コンテキストウィンドウをタスクに必要な最小セットに保てます。

**Claude へのプロンプト例（構造化 JSON ビルド結果を受け取るケース）**

```
XcodeBuild MCP を使ってスキームを MyApp でビルドしてください。
エラーが出た場合はファイル名・行番号・エラー内容を日本語でリストアップして、
修正案を提示してください。
```

xclaude-plugin の MCP が Xcode のログを構造化して渡すため、Claude が「ファイル X の Y 行目が原因」と正確にピンポイントで回答しやすくなります。

**同作者の `ios-simulator-skill` との使い分け**

- **シミュレータ操作のみ**（タップ・スワイプ・スクリーンショット）→ `conorluddy/ios-simulator-skill` が軽量でシンプル
- **ビルド〜テスト〜UI 操作を一貫してエージェントに任せる** → `xclaude-plugin` でワークフロー別 MCP を組み合わせる
