---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-08-12"
date: 2026-08-12 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- **v2.1.227（2026-08-10）リリース**（[Claude Code 変更ログ](https://code.claude.com/docs/en/changelog)）— 前日（08-11）記事の時点では v2.1.226 が最新と記載したが、同日中に v2.1.227 が公開されていた。iOS × Remote Control 運用に直結する修正が含まれる: ① **期限切れログイントークンでセッション開始した際にサブスクリプション階層なしでフィーチャーフラグが評価される問題を修正** — Max プランユーザーが Fable クレジットの有効化を誤って促されていた問題（Issue #85291 と関連する誤課金誘発バグ）が解消; ② **`allowed_non_write_users` を使った GitHub-hosted runners 上の `claude-code-action` で全 Bash コマンドが失敗する問題を修正** — XcodeBuildMCP の CI 自動化フローでも同様に影響していたケースが解消; ③ **スラッシュコマンドメニューの改善**（選択行のみ青色になり、マッチ文字が太字表示に）とイベントループストールの削減。

## 🛠 GitHub の動き

- [Issue #84295 — Mobile（iOS）: Remote Control の spawn_task チップとサイドバーがタップ不可（OPEN / 2026-08-05）](https://github.com/anthropics/claude-code/issues/84295) — iPhone の Claude iOS アプリおよび Safari（claude.ai/code）から Remote Control を操作すると、**会話中にインライン表示される spawn_task チップが一切タップできず、サイドバーのセッションリストも操作不能**になる問題。報告者が DOM を調査したところ、spawn_task チップは `pointer-events: none` のコンテナ内に描画されており、さらにサイドバーのインタラクティブ要素の 98.6%（69/70）が Apple の最小タッチターゲット 44px を下回る 26px 高・20×20px サイズであることが判明。デスクトップブラウザでは正常動作する。**回避策: Safari の「デスクトップ用 Web サイトを表示」＋ピンチズームでサイドバーを部分的に操作できる。spawn_task チップの代わりにチャットでテキスト入力してサブタスクを指示する。**

- [Issue #79991 — iOS Simulator パネルが Xcode 27（Device Hub）環境で動作しない（OPEN / 2026-07-22）](https://github.com/anthropics/claude-code/issues/79991) — Xcode 27 が `Simulator.app` を廃止して新しい **Device Hub** に移行し、`SimulatorKit.framework` のパスが `Contents/Developer/Library/PrivateFrameworks/` から `Contents/SharedFrameworks/` に変更されたことで、Claude Code の iOS Simulator パネルが `xcode-select` に Xcode 27 を指定した環境で完全に動作しなくなる問題。claude-ios-sim ヘルパーがフレームワークのパスをハードコードしているため `"Attempting to load a file at path 'Contents/Developer/Library/PrivateFrameworks/SimulatorKit.framework', but it does not exist"` エラーで失敗し続ける。前日（08-11）記事の Issue #84943（Xcode 26.6 上での誤検知）とは別問題で、こちらは **Xcode 27 beta のみが対象**。**回避策: `xcode-select -s /Applications/Xcode\ 26.app/Contents/Developer` で Xcode 26.x を指定するか、XcodeBuildMCP を使う（Xcode 27 SDK が必要なビルドとは xcode-select が競合する点に注意）。**

## 📝 日本語コミュニティ

- 該当なし（本日時点で直近 5 投稿（2026-08-07〜08-11）と重複しない新規の iOS / Swift / Xcode 関連記事は Zenn・Qiita にて確認できなかった）

## 🌐 海外コミュニティ / Tips

- **[anthropics/ClaudeForFoundationModels v0.1.0（GitHub）](https://github.com/anthropics/ClaudeForFoundationModels)** — Anthropic が WWDC 2026 翌日（2026-06-08）に公開した公式 Swift パッケージ。Apple が iOS 27 / macOS 27 向けに発表した **Apple Foundation Models フレームワーク**の `LanguageModel` プロトコルに Claude が準拠し、**`LanguageModelSession` の同一 API で on-device モデルと Claude を引数ひとつで切り替えられる**。ストリーミング・`@Generable` を使った型安全な構造化出力・サーバーサイドツール（Web 検索・コード実行）に対応。対象は iOS 27+ / macOS 27+ / visionOS 27+ / watchOS 27+（いずれも Xcode 27 beta が必要）。認証方式は App Attest（本番推奨）・API キー（Simulator 開発用）・プロキシバックエンドの 3 種類から選択可能で、リクエストは Apple を経由せず直接 Anthropic API に届く。iOS アプリから Claude を呼ぶ際の「公式の方法」として今後の標準になる見込み。

## 💡 今日のおすすめ実践 Tip

**「ClaudeForFoundationModels で iOS アプリに Claude を組み込む最小構成 — on-device モデルとの切り替えパターン」**

WWDC 2026 で Apple が発表した Foundation Models フレームワークは、`LanguageModel` プロトコルを実装するバックエンドを引数ひとつで差し替えられる設計になっています。`anthropics/ClaudeForFoundationModels` パッケージを使うと、**既存の on-device モデル利用コードをほぼそのまま維持しながら Claude に切り替える**ことが可能です。

**インストール（Swift Package Manager）**

```swift
// Package.swift
dependencies: [
    .package(url: "https://github.com/anthropics/ClaudeForFoundationModels.git", from: "0.1.0")
]
```

**最小構成（Simulator 開発向け・API キー認証）**

```swift
import FoundationModels
import ClaudeForFoundationModels

// on-device モデル（無料・オフライン）
let onDeviceSession = LanguageModelSession()

// Claude Sonnet 5（API キー認証 — Simulator 開発時のみ）
let claudeModel = ClaudeLanguageModel(
    name: .sonnet5,
    auth: .apiKey(ProcessInfo.processInfo.environment["ANTHROPIC_API_KEY"] ?? "")
)
let claudeSession = LanguageModelSession(model: claudeModel)

// 呼び出し方はどちらも同じ
let response = try await claudeSession.respond(to: "このコードの問題点を教えて")
print(response.content)
```

**本番アプリでの推奨: App Attest 認証**

API キーをアプリにバンドルすることは避けてください。本番では `auth: .appAttest(...)` を使い、デバイス固有の署名でリクエストを Anthropic API に直接送ります（Apple は通信経路に入りません）。

**on-device ↔ Claude の切り替え指針**

| ユースケース | 推奨 |
|---|---|
| オフライン対応・低レイテンシ・プライバシー優先 | on-device（`LanguageModelSession()` デフォルト） |
| 多段推論・コード生成・長文処理・Web 検索が必要 | Claude Sonnet 5 / Opus 5 |
| コスト最適化（シンプルな分類・要約） | on-device → 必要な場合のみ Claude へフォールバック |

`ClaudeForFoundationModels` が iOS 27 / Xcode 27 を必須とする点に注意。現時点ではベータ版であり、OS 27 の一般リリースに合わせて GA になる予定です。Claude Code で iOS アプリ自体を開発しながら、そのアプリ内に `ClaudeForFoundationModels` を組み込む二重の AI 活用が今後の標準的な開発スタイルになりそうです。
