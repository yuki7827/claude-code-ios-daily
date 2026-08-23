---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-08-24"
date: 2026-08-24 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- **v2.1.241（2026-08-23）リリース**（[Claude Code 変更ログ](https://code.claude.com/docs/en/changelog)）— バグ修正と信頼性の改善のみ。新機能なし。iOS 固有の変更点は含まれない。

## 🛠 GitHub の動き

- [Issue #88504 — iOS Simulator パネルが macOS 27 beta でクラッシュループ: seatbelt プロファイルが Metal の新しい Cache ディレクトリへの書き込みを拒否（OPEN / 2026-08-21）](https://github.com/anthropics/claude-code/issues/88504) — macOS 27 beta (26A5416b) 上で iOS Simulator パネルを起動しようとすると `*** -[__NSPlaceholderArray initWithObjects:count:]: attempt to insert nil object from objects[0]` が発生し、クラッシュループに陥る問題。根本原因は macOS 27 beta で Metal が `recordBinaryArchiveUsage:` を呼び出すようになり、その書き込み先が `DARWIN_USER_CACHE_DIR`（`/var/folders/.../C/`）になったが、`claude-ios-sim` ヘルパーの seatbelt プロファイルにこのパスへの書き込み許可が含まれていないこと。CoreImage がプリコンパイル済みカーネルアーカイブを読み込む際に Metal が nil のファイルハンドルを返し、NSArray 初期化がクラッシュする。**現時点の回避策**: `Claude.app/Contents/Resources/claude-ios-sim.sb` を手動でテキストエディタで開き、`(allow file-write* (subpath (param "DARWIN_CACHE")))` の 1 行を追加してプロファイルをパッチする（アプリ更新時に上書きされるため、更新のたびに再適用が必要）。macOS 26.x を維持している環境では影響なし。

- [Issue #87520 — git shim がサンドボックス内で xcode-select エラーに衝突（OPEN / 2026-08-18・2026-08-23 更新）](https://github.com/anthropics/claude-code/issues/87520) — macOS のサンドボックスモードを有効にした状態（特に AWS Bedrock 経由）で git を使うと `xcode-select: error: unable to read data link at '/var/select/developer_dir', expected symbolic link (Operation not permitted)` が断続的に発生し git 操作が失敗する問題。サンドボックスがシステムレベルのシンボリックリンク `/var/select/developer_dir` の読み取りを禁止するため、Developer Tools の場所を xcode-select が解決できなくなる。v2.1.233 で発生した退行バグ。**現時点の回避策**: ① `claude --no-sandbox` でサンドボックスを無効にする（セキュリティリスクを許容できる場合のみ）、② または読み取り許可のスコープをルート（`/`）まで広げる。

## 📝 日本語コミュニティ

- [Claude Code に iOSアプリの E2Eテスト までやらせるようにした（iOSシミュレータ + AXe）（Qiita / peka2）](https://qiita.com/peka2/items/9ce150b3b480516fc16e) — Claude Code が `xcrun simctl` でビルド・起動はできるもののタップ・スワイプ操作に詰まる問題を、AXe（iOS Simulator のアクセシビリティ API を叩く CLI ツール）と組み合わせることで解消した事例。ビルド→インストール→起動→AXe で UI 操作→結果検証までの E2E ループを Claude Code に全面委託する構成を解説している。アクセシビリティ ID を座標の代わりに使うため、画面サイズ変更やレイアウト変更に強いテストを書けるのが特徴。

## 🌐 海外コミュニティ / Tips

- [claude-code-apple-skills（rshankras / GitHub・2026-08-20 更新）](https://github.com/rshankras/claude-code-apple-skills) — iOS・macOS・iPadOS・visionOS・watchOS の開発を横断する 164 スキル・23 カテゴリのスキルコレクション。主要カテゴリは「Generators（63 スキル）」「Product（14）」「Testing（12）」「App Store（12）」「Design（7）」など。最近追加された注目スキルは Apple Intelligence 対応（Foundation Models・Visual Intelligence の 3 スキル）・visionOS 空間デザイン・SwiftUI 3D チャート・AlarmKit 連携・外部購入（0% 手数料 Web チェックアウト）など iOS 26+ 固有機能をカバー。スキルのインストール: `/plugin marketplace add rshankras/claude-code-apple-skills`。全投稿での初掲載。

## 💡 今日のおすすめ実践 Tip

**「macOS 27 beta の claude-ios-sim クラッシュ（Issue #88504）— seatbelt プロファイルを 1 行パッチして iOS Simulator パネルを復活させる」**

macOS 27 beta に移行後、Claude Code Desktop の iOS Simulator パネルが起動直後にクラッシュして使えなくなるケースが報告されています（Issue #88504）。原因は macOS 27 beta で Metal が書き込みを必要とするようになったキャッシュディレクトリが、`claude-ios-sim` の seatbelt プロファイルで許可されていないことです。

**影響を受ける環境の確認**

```bash
# macOS バージョン確認（26A5416b 以降なら影響を受ける可能性がある）
sw_vers -productVersion
sw_vers -buildVersion
```

**回避策: seatbelt プロファイルを手動パッチ（1 行追加）**

```bash
# claude-ios-sim.sb をバックアップ
sudo cp /Applications/Claude.app/Contents/Resources/claude-ios-sim.sb \
        /Applications/Claude.app/Contents/Resources/claude-ios-sim.sb.bak

# 現在の末尾を確認（最後の閉じカッコを見つける）
tail -5 /Applications/Claude.app/Contents/Resources/claude-ios-sim.sb
```

追加する 1 行をファイル末尾の `)`（最後の閉じカッコ）の直前に挿入します:

```scheme
;; macOS 27 beta: Metal が binary-archive の使用状況をユーザーキャッシュに書き込む
(allow file-write* (subpath (param "DARWIN_CACHE")))
```

実際の作業は `sudo nano` や `sudo vim` で直接編集してください。

```bash
# 編集後に Claude Desktop を再起動（再起動しないと変更が反映されない）
killall Claude 2>/dev/null; open /Applications/Claude.app
```

**注意事項**
- Claude Code のアップデートで `claude-ios-sim.sb` が上書きされると再度パッチが必要になります
- macOS 26.x 以前の環境ではこの問題は発生しないため対処不要です
- 公式修正が入るまでの暫定対処であり、将来のリリースで自動的に解消される予定です

**git のサンドボックスエラー（Issue #87520）も同時に発生している場合**

macOS 27 beta + サンドボックス有効環境では git エラーも重なることがあります。短期的には `claude --no-sandbox` でサンドボックスを無効にすることで git 操作は通りますが、セキュリティ上の影響を考慮の上で判断してください。本番の iOS 開発プロジェクトでは、sandbox を維持したまま xcode-select の修正リリースを待つことを推奨します。
