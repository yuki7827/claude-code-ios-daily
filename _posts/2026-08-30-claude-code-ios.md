---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-08-30"
date: 2026-08-30 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- 該当なし（2026-08-30 時点で v2.1.251〈2026-08-28〉以降の新規リリースは確認できず。[Claude Code 変更ログ](https://code.claude.com/docs/en/changelog)を参照）

## 🛠 GitHub の動き

- [Issue #58214 — `autoAllowBashIfSandboxed` が `xcodebuild -destination 'platform=iOS Simulator,...'` で誤って permission prompt を出すバグが修正（CLOSED / 2026-08-29）](https://github.com/anthropics/claude-code/issues/58214) — `sandbox.enabled: true` かつ `autoAllowBashIfSandboxed: true` の設定下で、`xcodebuild -destination 'platform=iOS Simulator,name=iPhone 16 Pro'` のような引数に含まれる `platform=iOS` が Bash 変数代入（`identifier=value` パターン）と誤判定され、自動承認されずに permission prompt が出ていた長年のバグが 2026-08-29 にクローズされた。`FOO=bar swift --version` や `xcodebuild -destination 'platform=iOS'` など、iOS 開発で日常的に使うコマンドが影響を受けており、エージェントがなぜ止まるか把握できずに誤った回避策（シェルスクリプトへのラッピング等）を取るケースが多く報告されていた。修正が入ったバージョンへのアップデートを推奨。

- [Issue #88504 — macOS 27 beta の seatbelt プロファイルが Metal のキャッシュディレクトリ書き込みを拒否 → iOS Simulator パネルがクラッシュループ（OPEN / 2026-08-21 起票）](https://github.com/anthropics/claude-code/issues/88504) — macOS 27 beta (26A5416b) で iOS Simulator パネルの `claude-ios-sim` ヘルパーが CoreImage の `CIContext` 初期化時に必ずクラッシュする原因が、この issue で初めて特定された。根本原因は `claude-ios-sim.sb`（seatbelt プロファイル）が `DARWIN_USER_CACHE_DIR`（`/var/folders/.../C/`）への書き込みを許可していないため、macOS 27 から追加された Metal の `recordBinaryArchiveUsage:` がキャッシュ書き込みを試みた際に `nil` オブジェクトが生成されて `NSInvalidArgumentException` が uncaught のまま `abort()` になるという流れ。投稿者は最小 Swift 再現コードで seatbelt の有無によって再現・非再現を確認し、修正案として「seatbelt プロファイルに `(allow file-write* (subpath (param "DARWIN_CACHE")))` を 1 行追加する」ことを提案。現時点では OPEN（未修正）だが、Issue #80177（同一現象の別報告）と合わせて Anthropic の対応が期待される。

## 📝 日本語コミュニティ

- 該当なし（本日確認できた最新の Zenn / Qiita 記事は直近 5 投稿で既出のものとの重複が確認できず、かつ新規公開分の直接アクセスが制限されていたため見送り）

## 🌐 海外コミュニティ / Tips

- [swift-ios-skills（dpearson2699 / GitHub、1.0k stars）](https://github.com/dpearson2699/swift-ios-skills) — iOS 26+・Swift 6.3・SwiftUI 向けのエージェントスキル 86 本をまとめたコレクション。SwiftUI（10 本: フォーカス管理・アニメーション・ジェスチャー・Liquid Glass・ナビゲーション）・Core Swift（10 本: API 設計・アーキテクチャ・Codable・Swift Testing）・App Experience Frameworks（13 本: Dynamic Island・App Intents・StoreKit・ウィジェット）・Data & Services（8 本: CloudKit・HealthKit・Apple Pay）・AI & ML（5 本: Foundation Models・Core ML・自然言語処理）・iOS Engineering（14 本: デバッグ・パフォーマンス・セキュリティ・ローカライズ）・Hardware（7 本: Bluetooth・NFC・AR）・Platform Integration（10 本: ホームオートメーション・権限管理）・Gaming（4 本: GameCenter・SceneKit）の 9 カテゴリ構成。全投稿での初掲載。

- [apple-skills（vabole / GitHub、323 stars）](https://github.com/vabole/apple-skills) — Claude Code・Codex 向けに iOS 26+ と Swift 6 を対象とした最新 API ドキュメントとワークフローガイドを提供するリポジトリ。SwiftUI・UIKit・HealthKit・MapKit のフレームワーク参照、Human Interface Guidelines と Liquid Glass デザインシステムに基づく設計指針、async/await・Swift Testing を使ったワークフロー、シミュレータ管理と App Store 最適化ユーティリティを収録。「`@Observable` マクロを使う・`NavigationStack` を使う」などモデルの学習データが古い場合に陥りがちな deprecated パターンを明示的に上書きし、現行の慣用 Swift コードを生成させることを目的とした設計が特徴。全投稿での初掲載。

## 💡 今日のおすすめ実践 Tip

**「Issue #58214 修正を機に：`autoAllowBashIfSandboxed` + `xcodebuild` の正しい設定とアップデート確認」**

Issue #58214 のクローズにより、`xcodebuild -destination 'platform=iOS Simulator,...'` をサンドボックス有効環境で実行しても permission prompt が出なくなりました。これは iOS 開発者にとって日常ワークフローを大きく改善する修正です。修正版へのアップデートと、設定の見直し手順を整理します。

**まずバージョンを確認・更新する**

```bash
claude --version
claude update
claude --version  # 修正が含まれるバージョン以降であることを確認
```

**`autoAllowBashIfSandboxed` の推奨設定（`.claude/settings.json`）**

```json
{
  "sandbox": {
    "enabled": true,
    "autoAllowBashIfSandboxed": true
  }
}
```

修正後は以下のコマンドが permission prompt なしで自動実行されるようになります:

```bash
# ✅ 修正後は自動承認される（以前は prompt が出ていた）
xcodebuild -destination 'platform=iOS Simulator,name=iPhone 16 Pro' -scheme MyApp build
xcodebuild -destination 'platform=iOS Simulator,OS=latest,name=iPad Pro 13-inch (M4)' test

# ✅ 環境変数設定も同様に修正済み
FOO=bar swift --version
SWIFT_DETERMINISTIC_HASHING=1 swift test

# ✅ simctl も xcodebuild 同様に安全
xcrun simctl boot "iPhone 16 Pro"
xcrun simctl install booted /path/to/MyApp.app
```

**`autoAllowBashIfSandboxed` の仕組み（改めて整理）**

- **サンドボックス内で実行**: コマンドはサンドボックスの制約の中で動作するため、作業ディレクトリ外への書き込み等は引き続き制限される
- **静的解析で判断できないコマンドは従来 prompt**: 修正前は `key=value` パターンを含むコマンドがこれに該当していた
- **修正後の変更点**: `key=value` パターンを含む引数を Bash 変数代入と誤判定しなくなった

**Issue #88504 の seatbelt クラッシュ（macOS 27 beta）はまだ未修正**

`autoAllowBashIfSandboxed` の修正とは別に、macOS 27 beta 環境では Issue #88504（seatbelt プロファイルが Metal のキャッシュディレクトリ書き込みを拒否）による iOS Simulator パネルのクラッシュは引き続き発生します。Anthropic の修正を待つ間の暫定回避策（コマンドラインツールで代替）:

```bash
# パネルが使えない間は xcrun simctl + XcodeBuildMCP で代替
# スクリーンショット
xcrun simctl io booted screenshot /tmp/screenshot.png

# アプリのビルド・インストール・起動
xcodebuild -scheme MyApp \
  -destination 'platform=iOS Simulator,name=iPhone 16 Pro' \
  build install

xcrun simctl launch booted com.example.MyApp
```

XcodeBuildMCP の `screenshot`・`snapshot_ui`・`tap`・`swipe` ツールも引き続き利用可能（Issue #88504 のコメントでも推奨されています）。
