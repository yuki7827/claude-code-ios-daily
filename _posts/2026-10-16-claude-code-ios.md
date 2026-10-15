---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-10-16"
date: 2026-10-16 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- 該当なし（2026-10-16 時点で直近の v2.1.300 は 10-15 キャッチアップ済み。v2.1.301 以降のリリースは未確認）

## 🛠 GitHub の動き

- 該当なし（2026-10-16 時点で新規 iOS / Xcode / Swift 関連 Issue の追加・クローズは確認できず）

## 📝 日本語コミュニティ

- 該当なし（2026-10-16 時点で Zenn / Qiita に iOS 文脈の新規記事は確認できず）

## 🌐 海外コミュニティ / Tips

- **[shobhit99/claude-watch — Apple Watch で Claude Code のターミナル出力確認・パーミッション承認・音声コマンドを行う OSS（GitHub）](https://github.com/shobhit99/claude-watch)** — Node.js ブリッジ（Mac 側）＋ SwiftUI iPhone コンパニオンアプリ＋ SwiftUI watchOS アプリの 3 層構成で、Apple Watch から Claude Code セッションをリモート操作できるプロジェクト。iPhone と Mac が同一 Wi-Fi にあれば WCSession 経由でライブターミナル出力を Watch に中継し、パーミッション要求が来たときは Watch のボタンで承認・拒否が行える。長時間の xcodebuild や iOS テスト実行中に Mac から離れても watch で進捗監視できる点が iOS 開発者向けにマッチする。Claude Code CLI 2.1 以降・iPhone 17.0 以上・Apple Watch 10.0 以上が必要。最終コミット: 2026-04-24（450 stars / 41 forks）。

- **[iOS Simulator ペイン — プライバシー・制限事項の公式ドキュメント（code.claude.com）](https://code.claude.com/docs/en/desktop-ios-simulator)** — 7 月に公開ベータが始まった Claude Code Desktop の iOS Simulator ペインについて、公式ドキュメントが詳細な制限事項を明記。**スクリーンショットは Anthropic に送信・保持される**（通常の会話コンテンツと同じ保持ポリシー）ため、実在のアカウントでサインインしたデバイスを Claude に操作させないよう推奨。macOS の Accessibility・Screen Recording パーミッションは不要（Apple Simulator と直接通信するため）。**クラウド / SSH セッションでは使用不可**（ローカルセッション限定）。また Enterprise プランでは現在利用できず、Pro・Max・Team プランのみ対象。

## 💡 今日のおすすめ実践 Tip

**claude-watch を使って xcodebuild 実行中に Apple Watch で進捗を監視する**

大規模な iOS プロジェクトでは `xcodebuild clean build` が 10 分以上かかることもある。claude-watch を使えば、ビルド中に Mac から離れても Apple Watch で出力を確認し、パーミッション要求を承認できる。

**セットアップ手順（概要）:**

```bash
# 1. リポジトリをクローン
git clone https://github.com/shobhit99/claude-watch.git
cd claude-watch

# 2. Node.js ブリッジをインストール・起動
cd bridge
npm install
node server.js
```

iPhone アプリ・watchOS アプリは Xcode でビルドして実機にインストール（シミュレーターは不可）。`claude-watch.plugin` を Claude Code のプラグインとして読み込む場合：

```bash
claude plugins install ./claude-watch.plugin
```

**活用例（iOS CI の監視）:**

1. `claude "xcodebuild test -scheme MyApp -destination 'platform=iOS Simulator,name=iPhone 16' 2>&1"` を実行
2. テスト実行中に Watch で stdout をリアルタイム確認
3. `brew install` や `xcode-select --install` など sudo パーミッションが必要なステップも Watch から承認
4. テスト失敗サマリーが Watch 通知で届く

**注意点:**
- Mac と iPhone は同一 Wi-Fi 上に置くこと（異なるネットワークでは中継できない）
- ビルドログは非常に長くなるため、Watch の表示はスクロール操作が必要
- claude-watch はコミュニティ製 OSS であり、Anthropic の公式サポート外
- セキュリティ上、ブリッジサーバーはローカルネットワーク内のみで動作する設計になっている（外部への公開は非推奨）
