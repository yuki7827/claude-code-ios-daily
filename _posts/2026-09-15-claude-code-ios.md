---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-09-15"
date: 2026-09-15 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- 該当なし（2026-09-15 時点の最新版は引き続き v2.1.270。9/12 リリース以降、新しい Claude Code リリースは確認されていない）

## 🛠 GitHub の動き

- 該当なし（前日までに報告した Issue #93949・#93823・#92442・#79991 に新たなコメント・修正は確認できず。いずれも OPEN のまま。引き続き xcrun simctl / XcodeBuildMCP での回避が推奨される）

## 📝 日本語コミュニティ

- 該当なし（2026-09-15 時点で新規の Zenn・Qiita 記事は確認できず）

## 🌐 海外コミュニティ / Tips

- **[Xcode 27.0 正式リリース（2026-09-14）— Apple Silicon 専用・Swift 6.4・ネイティブ AI エージェント搭載（Apple Developer Releases）](https://developer.apple.com/news/releases/?id=09092026h)** — RC（build 27A266a）がそのまま正式版として確定し、macOS 27.0（build 26A428）と同日公開。Xcode 史上初めて Intel Mac を完全非対応とし、Apple Silicon のみで動作。Swift 6.4・iOS 27 / iPadOS 27 / macOS 27 / tvOS 27 / visionOS 27 SDK を同梱。最大の変化は **Claude・Gemini・GPT-4o** をネイティブエージェントとして搭載した点で、エージェントが Device Hub 経由でシミュレーターを起動・アプリをインストール・タッチイベントを合成・スクリーンショット撮影できるようになった。また、エージェントのプランニング結果が編集可能な Markdown アーティファクトとして IDE 内に表示されるようになり（first-class）、インライン補完は Neural Engine でオフデバイス処理（ソースコード非送信）、重いエージェント処理のみクラウドへルーティングする設計になっている。App Store への iOS 27 対応アプリの提出も同日から受付開始。

- **[superagents-lab/xcode27-skills — Xcode 27 の Agent Skills を Claude Code・Codex・Cursor で使えるように公開（github.com/superagents-lab）](https://github.com/superagents-lab/xcode27-skills)** — Xcode 27 に内蔵されている公式 Agent Skills（Apple 自身が書いた SwiftUI ガイダンス等）を CLI で手軽にインストールできるパッケージ。エクスポートされる 7 スキルは `swiftui-specialist`（SwiftUI のベストプラクティス）、`whats-new-in-swiftui`（2026 年新 API 採用ガイド）、`uikit-modernization`（UIKit → SwiftUI 移行）、`swift-testing`（Swift Testing フレームワーク）、`c-bounds-safety`（C コードの境界安全性）、`security-hardening`（セキュリティ強化）、`accessibility`（アクセシビリティ対応）の計 7 種類。`xcrun agent skills export ~/.agents/skills` でローカルにエクスポートするか、`npx skills add superagents-lab/xcode27-skills --skill swiftui-specialist` で個別インストール可。Claude Code は起動時に `~/.agents/` フォルダを自動検出するため、エクスポート後に `/clear` または再起動するだけでスキルが有効になる。

## 💡 今日のおすすめ実践 Tip

**「Xcode 27 正式リリース記念 — Apple 公式 Agent Skills を Claude Code に取り込む 3 ステップ」**

Xcode 27.0 の正式リリース（2026-09-14）に伴い、Apple が内製した SwiftUI・セキュリティ・テスト等の Agent Skills が一般利用可能になりました。これを Claude Code に取り込むと、Apple が推奨する実装パターンを Claude が直接参照しながらコードを生成できるようになります。

**ステップ 1: Xcode 27 の Agent Skills をエクスポート**

```bash
# Xcode 27 付属の xcrun を使ってスキルを ~/.agents/skills にエクスポート
xcrun agent skills export ~/.agents/skills

# エクスポートされたスキルを確認
ls ~/.agents/skills/
# swiftui-specialist  whats-new-in-swiftui  uikit-modernization
# swift-testing  c-bounds-safety  security-hardening  accessibility
```

**ステップ 2: Claude Code を再起動してスキルを認識させる**

```bash
# 既存セッションがある場合は /clear を実行するか、
# 新しいターミナルで Claude Code を起動する
claude
# 起動ログに「Loaded 7 skills from ~/.agents/skills」と表示される
```

**ステップ 3: CLAUDE.md にスキル活用の指針を追記**

```markdown
## Agent Skills（Xcode 27 公式）
- swiftui-specialist: SwiftUI コード作成・レビュー・リファクタリング時は必ずこのスキルを参照する
- swift-testing: 新規テスト追加時は XCTest ではなく Swift Testing を使うこと（Apple 公式推奨）
- uikit-modernization: 既存 UIKit コードを SwiftUI に移行する際はこのスキルに従う
```

**動作確認**

```
# Claude Code に指示してスキルが活用されているか確認する例
「SwiftUI で検索バー付きのリスト画面を実装してください（swiftui-specialist スキルを参照して）」
```

Xcode 27 からは IDE 内エージェントと Claude Code CLI の 2 経路で Apple の同じスキルを参照できるようになりました。IDE 内エージェントは Xcode Preview・Device Hub・Swift Playgrounds との密な統合が強みで、Claude Code CLI は長時間のビルドエラー修正ループや複数ファイルにまたがるリファクタリングに強みがあります。両者を CLAUDE.md で明確に使い分けることで開発効率を最大化できます。
