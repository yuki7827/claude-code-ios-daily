# Claude Code × iOS 日次キャッチアップ — 手順書

このリポは「Claude Code を iOS 開発で活用するためのノウハウ」を毎朝 8:00 JST に自動キャッチアップして GitHub Pages サイトとして公開しています。
このファイルは、日次 Routine（リモート）と手動実行の両方が従う手順書です。

公開サイト: https://yuki7827.github.io/claude-code-ios-daily/

---

## 🔒 セキュリティ規約（最優先・絶対遵守）

このリポは **Public** です。以下を絶対にコミット・push してはいけません。

- API キー、アクセストークン、Cookie、認証情報、`.env` の中身
- メールアドレス、電話番号、住所などの個人を特定できる情報
- 社内情報、未公開のプロジェクト情報、private リポの URL や内容
- ローカルマシンのパス（`/Users/<実名>/...` 等）が含まれるログ
- スクリーンショット、画像（テキストでまとめる方針）

**収集するのは「公開ウェブで誰でも読める情報」だけ**にしてください。引用は必ず URL を添える形にし、URL から再現できない情報は載せないこと。

不安があれば**書かない**を選ぶ。コミット前に diff を `git diff --staged` で確認し、上記に該当しそうな文字列があれば中止して人間に確認を求める。

---

## やること（毎日の実行手順）

1. **本日の日付（JST）を取得**
   ```bash
   TZ=Asia/Tokyo date +%Y-%m-%d
   ```
2. **既存記事の確認**: `ls _posts/ | tail -5` で直近の投稿を読み、既出ネタ（同 URL / 同タイトル）を把握
3. **情報収集**: 下の「情報源」リストに沿って WebSearch / WebFetch / `gh` で調査
4. **重複排除**: 既出ネタは捨て、新規ネタのみ採用
5. **記事作成**: `_posts/{YYYY-MM-DD}-claude-code-ios.md` を作成（Jekyll 形式・テンプレートは下記）
6. **セキュリティ確認**: 上の「セキュリティ規約」に違反する内容が混入していないか `git diff` で確認
7. **commit & push**:
   ```bash
   git add _posts/{YYYY-MM-DD}-claude-code-ios.md
   git commit -m "Add catchup for {YYYY-MM-DD}"
   git push
   ```
8. **新規ネタ 0 件の日**: それでも記事は作るが、本文は「本日は新規ネタなし」の 1 行のみ

---

## 情報源（優先順）

### 1. Anthropic 公式
- docs.claude.com（Claude Code セクション）の更新
- anthropic.com/news の Claude Code 関連投稿
- `gh release list --repo anthropics/claude-code --limit 5`
- `gh api repos/anthropics/claude-code/releases/latest`

### 2. GitHub の動き（iOS フィルタ）
- `gh search issues --repo anthropics/claude-code "swift OR xcode OR ios" --limit 10 --sort updated`
- `gh search repos "claude-code ios" --limit 10`
- claude-code 用のサードパーティ skill / hook / MCP で iOS 関連のもの（XcodeBuildMCP など）

### 3. 日本語コミュニティ
- Zenn: `Claude Code iOS` / `Claude Code Swift` / `Claude Code Xcode`
- Qiita: 同上
- はてなブックマーク: 同上

### 4. 海外コミュニティ / Tips
- r/ClaudeAI / r/iOSProgramming
- dev.to / Hacker News（`Claude Code` で検索し iOS 文脈を抽出）

## 検索クエリ候補

```
Claude Code iOS / Swift / Xcode / SwiftUI / TCA / fastlane / XcodeBuildMCP
```

---

## 記事テンプレート（Jekyll 投稿）

ファイル名: `_posts/{YYYY-MM-DD}-claude-code-ios.md`

```markdown
---
layout: post
title: "Claude Code × iOS キャッチアップ {YYYY-MM-DD}"
date: {YYYY-MM-DD} 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート
- [タイトル](URL) — 1〜2行で要点（iOS 開発にどう効くか一言）

## 🛠 GitHub の動き
- [Issue/PR タイトル](URL) — 要点

## 📝 日本語コミュニティ
- [記事タイトル](URL) — 要点

## 🌐 海外コミュニティ / Tips
- [記事/投稿タイトル](URL) — 要点

## 💡 今日のおすすめ実践 Tip
（その日の収穫から実務で使えそうなものを 1 つ、3〜5 行で）
```

各セクションが空なら、見出しごと省略して構いません。

---

## ルール

- **iOS 開発との接点が薄いネタは入れない**（汎用 Claude Code 話題は除外。SwiftUI / Xcode / TestFlight / fastlane / TCA / XcodeBuildMCP 等の文脈に絞る）
- **URL は必ず付ける**（後で再訪できないと価値が下がる）
- **要点は 1〜2 行**（長文要約は不要）
- **重複排除**: 直近 5 投稿を読み、同 URL or 同タイトルが既出なら採用しない
- **新規 0 件の日でも記事は作る**（連続性が分かるように）
- **commit メッセージ**: `Add catchup for YYYY-MM-DD` で統一
