---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-11-13"
date: 2026-11-13 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- 該当なし（2026-11-13 時点で v2.1.322 以降の新規リリースは確認できず。引き続き [公式 Changelog](https://code.claude.com/docs/en/changelog) を確認のこと）

## 🛠 GitHub の動き

- [dpearson2699/swift-ios-skills（GitHub・v3.7.0）](https://github.com/dpearson2699/swift-ios-skills) — iOS 26+・Swift 6.3・SwiftUI および最新 Apple フレームワーク向けの Claude Code スキルパック。全 84 スキルを `npx skills add dpearson2699/swift-ios-skills --all` で一括導入できる。PermissionKit・EnergyKit・PaperKit など iOS 26 β フレームワーク対応スキルも収録。v3.7.0 で Swift 6.2 → 6.3 対応に全スキルをバージョンアップ。ライセンスは PolyForm Perimeter のため商用利用時は確認が必要。

## 📝 日本語コミュニティ

- [Apple スタイルの UI ルールを Claude Code スキルにエンコードする（Zenn / tmasuyama1114）](https://zenn.dev/tmasuyama1114/articles/apple_design_skills) — `SKILL.md` に Apple HIG 準拠のデザインルールを記述し Claude Code エージェントに渡すことで、コード生成時にも Apple らしい UI コンポーネント設計を維持できるアプローチを実例付きで解説。SwiftUI の色・フォント・スペーシングのルールを自然言語で定義する方法が具体的に示されており、デザインとコードの乖離を防ぐガードレールとして活用できる。

## 🌐 海外コミュニティ / Tips

- [I'm not a programmer. I built and launched an app from my iPhone with Claude Code.（dev.to / inuken）](https://dev.to/inuken/im-not-a-programmer-i-built-and-launched-an-app-from-my-iphone-with-claude-code-59mb) — プログラミング経験ゼロの著者が iPhone から Claude Code の iOS アプリ版経由でアプリを作成・App Store にリリースした実践レポート。毎日 1 時間程度の作業でリリースまで到達した過程と、CI/CD 設定など iPhone 単体では突破が難しかった工程の整理が参考になる。

- [Vibe Coding for Senior iOS Developers: 6 Takeaways After Shipping 10 Apps in 4 Months（dev.to / dragos_roua）](https://dev.to/dragos_roua/vibe-coding-for-senior-ios-developers-6-takeaways-after-shipping-10-apps-in-4-months-4la0) — 4 ヶ月で 10 本の iOS アプリをリリースしたシニア開発者による実践的な気づき。「最低 2 モデルを使い分ける」「生成時間が短縮した分、検証時間を 50% に増やす」「コードレビューの習慣をそのままモデルのミス検出に転用する」という 3 点が特に再現性が高いとされ、AI ツールに慣れた経験者向けの具体的な指針が整理されている。

- [How I Built and Shipped a Swift App with Claude Code（Hackernoon）](https://hackernoon.com/how-i-built-and-shipped-a-swift-app-with-claude-code) — Swift ゼロから Claude Code のみを使ってアプリを App Store に出荷した経験者エンジニアのレポート。「コンパイルは通るが iOS 的に危険なコード」を出力させない工夫として CLAUDE.md への制約明記と、`xcodebuild -destination 'platform=iOS Simulator'` によるヘッドレステストを組み合わせる手順が詳述されている。

## 💡 今日のおすすめ実践 Tip

**`dpearson2699/swift-ios-skills` のセキュリティスキルで安全な Swift コードを生成する**

`swift-ios-skills` パックには `swift-security` スキルが含まれており、これを有効にするだけで Claude Code がフォースアンラップ・任意の `try!`・ハードコードされた認証情報などの危険なパターンを避けたコードを生成するようになる。

```bash
# すべてのスキルを一括導入する場合
npx skills add dpearson2699/swift-ios-skills --all

# セキュリティスキルだけを使いたい場合は CLAUDE.md に直接参照を追記
# （プロジェクトルートの CLAUDE.md）
# skills:
#   - https://github.com/dpearson2699/swift-ios-skills/blob/main/swift-security.md
```

特に `swift-security` スキルは以下の制約を Claude Code に課す：
- `!` によるフォースアンラップの禁止（`guard let` / `if let` を使用）
- `UserDefaults` や環境変数への認証情報の直接保存を禁止
- Keychain アクセスに `kSecAttrAccessibleWhenUnlocked` を明示するよう要求
- ネットワーク通信には `URLSession.shared` ではなく専用の `NetworkClient` 経由を推奨

これを CLAUDE.md と組み合わせることで、App Store 審査前のセキュリティレビューで指摘される典型的な問題を事前に防げる。iOS 26 β フレームワーク（PermissionKit など）を使う場合は、対応スキルがまだ変更される可能性があるため `npx skills add --version 3.7.0` で固定しておくと安心。
