---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-06-28"
date: 2026-06-28 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Claude Code v2.1.195（2026-06-26）](https://github.com/anthropics/claude-code/releases/tag/v2.1.195) — iOS 開発者に直接影響する 3 つの修正を含む。①**日本語・中国語・タイ語など空白なし言語での音声自動送信バグを修正**：`/voice` コマンドで日本語を話したあとにスペースキーを放しても自動送信されなかった問題が解消され、日本語 iOS 開発者が音声入力でスムーズに Claude Code に指示できるようになった。②**ハイフンを含むフック識別子の部分一致バグを修正**：`PostToolUse.code-reviewer`・`mcp__brave-search` などの識別子が substring match で誤動作していたのを完全一致に変更。Claude Code フックを CI や fastlane と組み合わせているプロジェクトで意図しない二重起動が起きていた場合は設定を見直す。ワイルドカードは今後 `mcp__brave-search__.*` 形式で指定。③**`CLAUDE_CODE_DISABLE_MOUSE_CLICKS` 環境変数を追加**：フルスクリーンモードでマウスのクリック/ドラッグ/ホバーを無効化しつつスクロールだけを許容できる。ターミナルフルスクリーン中に Xcode や Simulator へマウスが誤って侵入する問題を防ぐ用途に使える。

## 🛠 GitHub の動き

- [rshankras/claude-code-apple-skills](https://github.com/rshankras/claude-code-apple-skills) — iOS・macOS・watchOS・visionOS 開発からApp Store 公開までをカバーする **139 スキル集**（うち 62 本は即デプロイ可能なコード生成スキル）。23 カテゴリに整理されており、ネットワーク層・StoreKit 2・WalletKit・アクセシビリティ・セキュリティ実装など「インディー Apple 開発者スタック」の知識レイヤーを担う。自動化エージェント・MCP 連携ツールの 2 リポジトリと組み合わせて使うことが前提の設計で、HIG 準拠チェックや UI 品質確保スキルも含む。

- [hmohamed01/swift-development](https://github.com/hmohamed01/swift-development) — SwiftUI 実装パターン（iOS 13+/17+）・NavigationStack・Swift 6 の async/await・Sendable・テストフレームワークを SKILL.md に収録した Claude Code スキル。ヘルパースクリプトとして「新規パッケージ作成・テスト実行・コード整形 Lint・シミュレータ管理」も付属し、xcodebuild ラッパーによるトークン効率の高い出力を提供する。単体で使えるシンプルな構成が特徴で、大型スキル集を使わず最小構成で始めたい iOS 開発者向け。

## 📝 日本語コミュニティ

- [Claude Code にシミュレータを渡したら自分でタップしてスクショで検証し始めた（Qiita / shimo4228）](https://qiita.com/shimo4228/items/3c6106d5407deea0cbf4) — XcodeBuildMCP の `tap`・`screenshot`・`snapshot_ui` ツールを使って Claude Code が iOS シミュレータを自律操作・検証する実践報告。アクセシビリティ ID でセマンティックに要素を特定するため座標依存がなく、画面遷移後のスクリーンショット比較まで自動化できる。Claude Code の指示を一度出すだけで「タップ → 画面確認 → 次のタップ」のループをエージェントが自律で回す。

- [iOS アプリ開発未経験者が Claude Code を使って 1 週間でリリースしたお話（Qiita / chatrate）](https://qiita.com/chatrate/items/bd2703d68a2bb8fffe08) — Swift 未経験からのゼロイチ開発で Claude Code を全面活用し 1 週間で App Store リリースに至った体験記。プロジェクト構成・CLAUDE.md 設計・XcodeBuildMCP との組み合わせなど「最小構成で実際に動いた」設定を公開。経験のない分野でのエージェント活用の実例として参考になる。

- [【初心者向け】VS Code の Claude Code で「iOS コードレビュー → 修正 → Xcode ビルド確認」を自動化する（Qiita / 4q_sano）](https://qiita.com/4q_sano/items/f29dba5fa92958850a32) — VS Code 拡張版 Claude Code を使い、Swift コードのレビュー・修正・xcodebuild によるビルド確認を一気通貫で自動化する手順を初心者向けに解説した記事。Xcode を開かず VS Code だけで iOS ビルドを確認できる構成の作り方を順を追って説明。

## 🌐 海外コミュニティ / Tips

- [GeorgeLyon/SwiftClaude](https://github.com/GeorgeLyon/SwiftClaude) — Anthropic の Claude API を Swift から直接呼び出す Swift 6 製 SDK（Swift Package Manager 対応・iOS 18+/macOS 15+）。`@Tool` マクロでツール定義が簡潔に書け、`UIImage`/`NSImage` をメッセージに直接渡せるビジョン対応、プロンプトキャッシングベータ対応。iOS アプリに Claude を組み込む際に ClaudeCodeSDK（Claude Code CLI ラッパー）ではなく Claude API を直接叩きたい場合の選択肢として有用。まだ v1.0 未満でありフィードバック収集中。

- [Claude Code with Swift: SwiftUI, SPM, Xcode Workflow（Claudify）](https://claudify.tech/blog/claude-code-swift) — Swift / SwiftUI × Claude Code のセットアップを徹底解説した英語ガイド。CLAUDE.md に必ず記載すべき 6 項目（Swift toolchain バージョン・UI フレームワーク方針・プロジェクトファイル管理ルール・並行処理モデル・SPM 依存関係の追加方法・ビルド&テストコマンド）を体系的に整理。「Swift は Claude Code と相性が悪いエコシステムではなく、正しく文脈を与えれば高品質な出力が得られる」という立場から、ハマりやすい落とし穴（UIKit vs SwiftUI 混在・Swift 6 concurrency 移行期）の対処法も収録。

## 💡 今日のおすすめ実践 Tip

**「v2.1.195 の日本語音声自動送信修正 + XcodeBuildMCP シミュレータ自律操作で、話しかけるだけの iOS 検証フローを作る」**

v2.1.195 で日本語のまま `/voice` を使って Claude Code に指示を送れるようになりました。これと XcodeBuildMCP のシミュレータ操作ツールを組み合わせると、「日本語で話しかける → Claude が自律的にシミュレータをタップ＆スクリーンショット → 問題があれば自分で修正してビルド」という検証ループが実現できます。

**セットアップ（.claude/settings.json）**

```json
{
  "voice": {
    "language": "japanese"
  },
  "allowedTools": [
    "Bash(xcodebuild:*)",
    "Bash(xcrun:*)",
    "mcp__XcodeBuildMCP__tap",
    "mcp__XcodeBuildMCP__screenshot",
    "mcp__XcodeBuildMCP__snapshot_ui",
    "mcp__XcodeBuildMCP__launch_app"
  ]
}
```

**実際の使い方**

```
# スペースキーを押したまま日本語で話す（v2.1.195 で自動送信が正常動作）

「ログイン画面を開いて、メールアドレスフィールドにタップして
 test@example.com と入力し、ログインボタンを押したあと
 ホーム画面が表示されているかスクリーンショットで確認して」
```

Claude Code が `launch_app` → `tap` → `snapshot_ui` → `screenshot` の順にツールを自律呼び出しし、ホーム画面への遷移を確認した結果だけを日本語で返してきます。テストケースを口頭で投げるだけで UI 回帰確認が自動化できる、最も手軽な iOS QA フローの一つです。
