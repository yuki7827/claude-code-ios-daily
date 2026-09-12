---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-09-13"
date: 2026-09-13 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- **v2.1.270（2026-09-12）リリース**（[Claude Code 変更ログ](https://code.claude.com/docs/en/changelog)）— v2.1.269 で混入したリグレッション修正が 1 件のみのパッチリリース。**Bash ツールで読み取り専用 git コマンド（`git log`・`git diff` など）が、セッションが一定時間動き続けた後にパーミッション確認を要求するようになるバグ**を修正。iOS 開発者が長時間の Claude Code セッション中に `git log` や `git status` を走らせると途中から許可ダイアログが出るようになっていた問題が解消される。なお本日時点での最新版は v2.1.270。

## 🛠 GitHub の動き

- **[Issue #93823（2026-09-12 作成 / OPEN）— Claude Desktop (macOS) の Code タブでシェルコマンド完了後にアプリ全体がフリーズ：swift_addon.node の UNUserNotification デッドロック](https://github.com/anthropics/claude-code/issues/93823)** — Claude Desktop の Code タブで `xcodebuild build`・`xcrun simctl launch` 等のシェルコマンドを実行すると、コマンド完了の瞬間にアプリ全体がロック（ウィンドウ操作不能・スピニングビーチボールなし）するバグ。内部では `swift_addon.node` の `NotificationService.close(id:)` が `UNUserNotificationCenter` への同期 XPC 呼び出しをメインスレッドでブロック待ちし、同時に別スレッドが通知の削除で同じキューを保持することでデッドロックが発生している（`sample` コマンドで 10 秒間・6885/6885 サンプル同一スタックを確認）。v2.1.269 での発生が確認されており、現時点で open のまま。**iOS 開発者向けの暫定回避策**: ターミナルコマンド完了時の通知を macOS の通知設定で Claude Desktop から無効化すること（システム設定 → 通知 → Claude → 通知をオフ）。なお CLI 版（`claude` コマンド単体）は無関係。

- **[Issue #92442（2026-09-06 作成 / OPEN）— macOS 27 beta で iOS Simulator ライブパネルヘルパーが Metal/CoreImage 初期化で 100% クラッシュ](https://github.com/anthropics/claude-code/issues/92442)** — macOS 27 beta + Apple Silicon (M4 Max) 環境で `claude-ios-sim` ヘルパーが Metal バイナリアーカイブ使用記録中（`_MTLDevice recordBinaryArchiveUsage:`）に NSArray へ nil 要素を挿入しようとして SIGABRT。45 件以上のクラッシュレポートが確認されており、一度も成功したことがないケースも報告されている。**他の Metal/CoreImage アプリは同一 macOS で正常動作**しているため、`claude-ios-sim` がリンクしている SDK バージョン（26.1 相当）と macOS 27 beta の Metal ランタイムの組み合わせ固有の問題と推測されている。**回避策**: `xcrun simctl io <udid> screenshot` や XcodeBuildMCP 経由でシミュレーターを操作（ライブパネル不使用）。複数の類似 Issue（[#90707](https://github.com/anthropics/claude-code/issues/90707)・[#90684](https://github.com/anthropics/claude-code/issues/90684)・[#80177](https://github.com/anthropics/claude-code/issues/80177)）が open で、修正対応中。

## 📝 日本語コミュニティ

- 該当なし（2026-09-13 時点で新規の Zenn・Qiita 記事は確認できず）

## 🌐 海外コミュニティ / Tips

- **[Claude Code iOS Simulator ドキュメント（code.claude.com）](https://code.claude.com/docs/en/desktop-ios-simulator)** — macOS 27 beta での `claude-ios-sim` クラッシュ問題（上述 Issue #92442 等）を踏まえ、公式ドキュメントの「Test iOS apps in the simulator」ページが注目を集めている。ページには現時点でサポートされている環境（Xcode 26.x + macOS 26.x）が明記されており、Xcode 27 / macOS 27 beta では**ライブパネルが未対応**であることが確認できる。iOS Simulator パネルの状態を正確に把握するには `desktop-features` の `iosSimulator.status` フィールドを参照する方法も紹介されており、`"unsupported"` の場合はパネルを使わず XcodeBuildMCP 等でフォールバックする設計指針が有用。

## 💡 今日のおすすめ実践 Tip

**「macOS 27 beta + Xcode 27 環境での Claude Desktop フリーズを最小化する設定（2026-09-13 時点）」**

本日明らかになった 2 件のバグ（swift_addon.node デッドロック・claude-ios-sim Metal クラッシュ）はいずれも **macOS 26.x + Xcode 26.x の安定版環境では再現しない**ため、iOS 開発者が Claude Desktop を業務利用する場合は当面ベータ OS を避けるのが最も安全です。それでも macOS 27 beta 環境を使い続ける必要がある場合は、以下の緩和策を組み合わせてください。

**① Claude Desktop の通知を無効化（Issue #93823 の回避）**

```bash
# macOS の通知を一括オフにしてデッドロックを防ぐ
defaults write com.anthropic.claudefordesktop NSUserNotificationAlertStyle none
# または: システム設定 → 通知 → Claude → 通知をオフ
```

**② iOS Simulator ライブパネルを諦め、XcodeBuildMCP + simctl を使う（Issue #92442 の回避）**

```markdown
## CLAUDE.md に追加する設定例（macOS 27 beta 環境）

## 環境制約（2026-09-13 時点）
- macOS 27 beta のため ios-sim ライブパネルは使用不可（Metal クラッシュ）
- シミュレーター操作はすべて以下で代替する:
  - ビルド: xcodebuildmcp の build ツール
  - 起動: xcrun simctl launch <UDID> <BundleID>
  - スクリーンショット: xcrun simctl io <UDID> screenshot /tmp/screen.png
  - UI 操作: XcodeBuildMCP の ui_tap / ui_type / wait_for_ui ツール
- Desktop の [Simulator] パネルは開かないこと（使おうとするとフリーズのリスク）
```

**③ v2.1.270 への更新を確認（v2.1.269 の git パーミッション regression 修正）**

```bash
claude --version   # 2.1.270 以上であることを確認
npm update -g @anthropic-ai/claude-code   # 最新版に更新
```

上記 3 点を組み合わせることで、macOS 27 beta 上でも Claude Code を使った iOS 開発フローを維持できます。修正リリースが出次第 CLAUDE.md の制約を段階的に外していくことを忘れずに。
