---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-11-19"
date: 2026-11-19 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [v2.1.326（2026-11-16）](https://code.claude.com/docs/en/changelog) — **Code Mode がエージェントチームと併用可能**になった。`OTEL_RESOURCE_INCLUDE_HOST` 環境変数を追加。起動時に不完全な許可ルールを警告するように変更。HIPAA 構成の組織では端末・VS Code セッションが既定でオートモードで起動するように変更（CI 環境への影響を確認推奨）。59 件の改善・233 件のバグ修正。

- [v2.1.327（2026-11-17）](https://code.claude.com/docs/en/changelog) — **`claude mcp add-json <name> -` で標準入力から JSON を読み込み可能**に（MCP のトークンやパスをコマンドライン履歴に残さず安全に設定できる）。`/status` コマンドが壊れたエージェント・スキル・コマンドの一覧を表示するように拡張。`/tui classic` モードを追加。VS Code でエクスプローラーの右クリックメニューから「Add to Chat」が使えるように。26 件の改善・100 件のバグ修正。

## 🛠 GitHub の動き

- [tarunyadav1/ios-app-lifecycle（GitHub）](https://github.com/tarunyadav1/ios-app-lifecycle) — 企画から App Store 公開後の運用まで iOS 開発の全工程をカバーする **16 スキル + 4 MCP サーバー**構成の Claude Code プラグイン（MIT）。`swiftui-liquid-glass`・`ios-app-intents`・`ios-debugger-agent`・`ios-monetization`（RevenueCat + StoreKit 2）・`ios-crash-monitoring-sentry`・`app-store-listing`（ASO）・`ios-post-launch` など iOS 特化スキルが充実。4 MCP サーバーは XcodeBuildMCP・Xcode MCP ブリッジ・RevenueCat・Mobbin。`/plugin marketplace add tarunyadav1/ios-app-lifecycle` 1 コマンドで導入可能。

## 📝 日本語コミュニティ

- 該当なし（本日時点で直近 5 記事と重複しない新規 Claude Code × iOS 記事は確認できず）

## 🌐 海外コミュニティ / Tips

- [I Let Claude Code Control My iPhones. Here's What It Can Actually Do.（dev.to / hisashispace）](https://dev.to/hisashispace/i-let-claude-code-control-my-iphones-heres-what-it-can-actually-do-2e7p) — 脱獄やサードパーティツールを使わず、**Xcode 標準の XCUITest + `xcrun devicectl`** だけで実機 iPhone を Claude Code から操作した実践報告。Apple 審査用の動画撮影・多言語 App Store スクリーンショット自動生成・iPhone 設定変更（Night Shift 切り替えなど）をユースケースとして検証。座標ではなくラベル名でタップするため誤操作が起きにくい設計になっており、テスト結果の録画は `xcrun xcresulttool` で取り出す。権限ダイアログは SpringBoard のアラートとして扱う工夫も実装されている。

## 💡 今日のおすすめ実践 Tip

**v2.1.327 の `claude mcp add-json ... -` でトークンをコマンドライン履歴に残さず MCP を安全に追加する**

XcodeBuildMCP など認証情報を持つ MCP サーバーを iOS CI 環境に追加する際、従来はコマンドラインに API キーや PAT を直接書く必要があり shell の履歴に残るリスクがあった。v2.1.327 以降は標準入力から JSON を渡す方式が使えるため、シークレットを環境変数経由で安全に注入できる。

```bash
# 例: 認証情報を環境変数経由で渡す
echo '{
  "type": "stdio",
  "command": "npx",
  "args": ["-y", "xcodebuildmcp@latest", "mcp"],
  "env": {
    "XCODE_MCP_TOKEN": "'"$XCODE_MCP_TOKEN"'"
  }
}' | claude mcp add-json xcodebuild -

# 追加済みの設定を確認
claude mcp list
```

GitHub Actions などの CI では `${{ secrets.XCODE_MCP_TOKEN }}` を環境変数に展開してから渡すことで、ログへのシークレット漏洩を防ぎながら MCP サーバーを設定できる。iOS チームで Claude Code を CI に組み込む場合は、この方式を標準セットアップとして CLAUDE.md に記載しておくと安全性が高まる。
