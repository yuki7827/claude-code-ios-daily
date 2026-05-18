---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-05-19"
date: 2026-05-19 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- 該当なし（2026-05-15 リリースの v2.1.143 が直近最新。5/18〜19 にかけて新リリースは確認できず）

## 🛠 GitHub の動き

- [AvdLee/SwiftUI-Agent-Skill v3.3.0](https://github.com/AvdLee/SwiftUI-Agent-Skill) (May 17, 2026) — Antoine van der Lee と Omar Elsayed 共作の SwiftUI ベストプラクティス Agent Skill（2.9k stars）。最新版で iOS 26 Liquid Glass の全バリアント・モーフィング・フォールバックパターンを追加。状態管理・ビュー合成・パフォーマンス・Swift Charts・macOS マルチウィンドウ・アクセシビリティを網羅し、Claude Code / Cursor など Agent Skills 対応ツールで即利用可能。

- [twostraws/SwiftUI-Agent-Skill v1.1.0](https://github.com/twostraws/SwiftUI-Agent-Skill) (Apr 20, 2026) — Paul Hudson（Hacking with Swift）作の SwiftUI AI コーディング矯正スキル（3.9k stars）。`NavigationView` 残留・過剰 `@State` 使用・`ForEach(array, id: \.self)` 多用など、LLM が生成しがちな旧来パターンを明示的に禁止し、モダンな `NavigationStack`・`@Observable`・Lazy スタック利用へ誘導する。MIT ライセンスで Claude Code / Codex / Gemini / Cursor 対応。

- [dpearson2699/swift-ios-skills v3.4.0](https://github.com/dpearson2699/swift-ios-skills) (Apr 26, 2026) — iOS 26+ / Swift 6.3 向け 84 スキル集（599 stars）。SwiftUI・Core Swift・App Experience・Data & Services・AI/ML・iOS Engineering・Hardware・Platform Integration・Gaming の 9 カテゴリを網羅。deprecated パターンを排除した自己完結型スキルで、Claude Code / Codex / Cursor / GitHub Copilot 対応。v3.0 で 19 スキル追加と全面再編成が行われた。

## 📝 日本語コミュニティ

- [Claude Code にシミュレータを渡したら自分でタップしてスクショで検証し始めた](https://qiita.com/shimo4228/items/3c6106d5407deea0cbf4) (shimo4228, Qiita, Mar 3, 2026) — XcodeBuildMCP の `snapshot_ui`・`ui-automation/tap`・`simulator/screenshot` を組み合わせ、ビルド→シミュレータ起動→UI 操作→スクショ画像認識による目視確認を Claude Code が自律実行した体験記。アクセシビリティ ID を `snapshot_ui` で取得 → 対象要素をタップ → スクリーンショットで表示内容を検証するループを構成し、人間が手動で行っていた UI テストの多くを代替できることを実証。UI 要素にアクセシビリティ ID を付けておくと Claude が安定してタップできる点も指摘。

## 🌐 海外コミュニティ / Tips

- [SwiftUI Agent Skill — Write better code with Claude, Codex, and other AI tools](https://www.hackingwithswift.com/articles/282/swiftui-agent-skill-claude-codex-ai) (Hacking with Swift, Paul Hudson) — `twostraws/SwiftUI-Agent-Skill` の公式解説記事。スキルが防ぐ典型的な LLM エラーパターンと Claude Code / Codex へのインストール手順を紹介。SwiftUI の「生成されやすいが間違っているコード」を具体的に列挙しており、プロジェクトの CLAUDE.md に禁止パターンとして転用する使い方も有効。

## 💡 今日のおすすめ実践 Tip

**SwiftUI Agent Skill を 2 種類組み合わせて「矯正」と「最新 API 参照」を両立させる**

`twostraws/SwiftUI-Agent-Skill`（LLM エラー矯正が主眼）と `AvdLee/SwiftUI-Agent-Skill`（iOS 26 Liquid Glass・パフォーマンス・Chart3D など最新 API リファレンスが主眼）はカバー範囲が相補的で、両方を同時に有効化すると効果が高い。

```bash
# グローバルスコープ（全プロジェクト共通）: 典型的エラー矯正
claude plugin install twostraws/SwiftUI-Agent-Skill

# プロジェクトスコープ（iOS 26 対応プロジェクト専用）: 最新 API リファレンス
claude plugin install AvdLee/SwiftUI-Agent-Skill
```

Claude Code は複数スキルを読み込む際にコンテキストを要約挿入するためレイテンシへの影響は限定的。iOS 26 への移行中のプロジェクトでは AvdLee 版の `swiftui-liquid-glass` スキルが Liquid Glass の全 3 バリアントとモーフィング API をカバーするため、古い `.regularMaterial` バックグラウンドへの退行を防げる。一方で twostraws 版が `NavigationStack` や `@Observable` への正しいマイグレーションを担う役割分担になる。iOS 26 の UI 刷新対応フェーズにある iOS 開発チームに特に有効な組み合わせ。
