---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-10-15"
date: 2026-10-15 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- **[v2.1.300（2026-10-14）リリース](https://code.claude.com/docs/en/changelog)** — Mod API 拡張（`virtualText`・`$.ui.view`・`prompt.change` フック）、OpenTelemetry GenAI 属性のサポート、`/advisor` コマンドが Claude Platform on AWS でも利用可能に。バックグラウンドコマンドが stop 要求から 1.5 秒後に強制終了されるよう変更（CI で xcodebuild を止め切れない問題への対処に効く）。`hooks.updatedInput` のバリデーションが追加され不明なキーが削除される。計 59 件のバグ修正。

- **[v2.1.299（2026-10-14）リリース](https://code.claude.com/docs/en/changelog)** — **`CLAUDE_CODE_DISABLE_FILE_MENTIONS`** 環境変数を追加：`@` メンション時にファイル内容ではなくパスのみを渡すモードが選択可能に（大型 Swift ファイルや `.xcodeproj` を丸ごと渡してコンテキストを浪費する問題を防ぐのに有用）。**アイドル時コンパクション**を導入：会話が 20 万トークンを超えた状態でユーザーが何も入力しない場合、1 時間のプロンプトキャッシュ TTL が切れる前に自動でコンパクションを実行するようになった（`"idleCompaction": false` で無効化可）。`/usage` に警告表示を追加、エージェントビューで `/effort` と `/fast` が使用可能に。計 131 件のバグ修正。

## 🛠 GitHub の動き

- **[Issue #95466（2026-09-18 作成 / OPEN）— iOS Simulator ツール: Xcode 27 アップグレード後に touch/tap インジェクションがサイレントな no-op になる（Simulator.app が headless DeviceHub に置き換え）](https://github.com/anthropics/claude-code/issues/95466)** — Xcode 27 で Apple が `Simulator.app` を廃止して `DeviceHub`（ヘッドレス常駐デーモン）に移行したことで、Claude Code の iOS Simulator ツールが送出する `touch`・`tap`・`swipe` などの入力インジェクションが内部的に no-op（何も起こらず、エラーも返さない）になるバグ。エラーが出ないまま操作が無視されるため、UI 自動化テストが「成功」として返却されても実際にはタップが届いていないというサイレント障害が起きやすい。Xcode 27 環境で Claude Code の iOS Simulator 機能を使っている開発者は全員影響を受ける可能性がある。ラベル: `bug`, `platform:macos`, `area:tools`。最終更新: 2026-10-14。

- **[Issue #100495（2026-10-08 作成 / OPEN）— iOS Simulator でサンドボックステスター iCloud サインインが拒否される](https://github.com/anthropics/claude-code/issues/100495)** — Claude Code のサンドボックスモードが有効な状態で iOS Simulator を使うと、StoreKit サンドボックステスター用の iCloud サインインが内部的にブロックされ、サブスクリプション・IAP 購入フローのテストができないバグ。TestFlight / App Store Connect のサンドボックス環境でのインアプリ購入を Claude Code に自動検証させているチームに影響する。ラベル: `enhancement`, `platform:macos`, `area:security`, `area:permissions`。

## 📝 日本語コミュニティ

- 該当なし（2026-10-15 時点で Zenn / Qiita に iOS 文脈の新規記事は確認できず）

## 🌐 海外コミュニティ / Tips

- **[apple-platform-build-tools-claude-code-plugin（kylehughes / GitHub）](https://github.com/kylehughes/apple-platform-build-tools-claude-code-plugin)** — iOS / macOS / tvOS / watchOS / visionOS すべてをカバーする Claude Code 公式プラグイン形式のビルドツール集。Agent Skill（`xrun` ツールチェーンのリファレンス）とサブエージェント（スキーム検出・シミュレーター選択・ビルド・結果レポート）で構成されており、生の `xcodebuild` ログをメインエージェントのコンテキストウィンドウに流し込まず、要約された結果のみを返す設計が特徴。単体の Swift Package には `swift build` / `swift test`、Xcode プロジェクトには `xcodebuild` を使い分ける。インストール: `npx -y skills add kylehughes/apple-platform-build-tools-claude-code-plugin --skill building-apple-platform-products --agent claude-code`

## 💡 今日のおすすめ実践 Tip

**`CLAUDE_CODE_DISABLE_FILE_MENTIONS` で大型 Swift ファイルのコンテキスト浪費を防ぐ**

v2.1.299 で追加された `CLAUDE_CODE_DISABLE_FILE_MENTIONS` 環境変数を使うと、`@FileName.swift` のメンションがファイル内容ではなくパスのみを渡すモードに切り替わる。大規模な iOS プロジェクトで Model や ViewModel が数百行に及ぶ場合、誤って `@LargeViewModel.swift` をメンションするだけでコンテキストが数千トークン消費されることがあった。この変数を有効にすればパス参照にとどまり、Claude が実際にファイルを読む必要があると判断した場合のみ `Read` ツールで取得するようになる。

```bash
# .env や CI の環境変数に追加
CLAUDE_CODE_DISABLE_FILE_MENTIONS=1
```

または Claude Code の設定ファイルで固定する場合：

```json
// .claude/settings.json
{
  "env": {
    "CLAUDE_CODE_DISABLE_FILE_MENTIONS": "1"
  }
}
```

特に以下のケースで効果が大きい：
- `.pbxproj` や `Package.resolved` を誤ってメンションしても内容が流れ込まない
- 大型の `Generated.swift`（SwiftGen / Sourcery 生成ファイル）のメンション誤爆を防ぐ
- CI 上でコスト管理を徹底したい場合（コンテキスト削減 → キャッシュヒット率向上）

なお、`"idleCompaction": false` を設定していないチームは v2.1.299 からアイドル時に自動コンパクションが走るようになる。長時間の iOS ビルド待ち中に会話が圧縮されてコンテキストが変わることがあるため、挙動が気になる場合は明示的に無効化しておくとよい。
