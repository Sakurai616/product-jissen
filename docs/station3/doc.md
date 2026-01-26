# 設計1

## 技術選定

## 🔧 フロントエンド

| 技術 | 用途 | 理由 |
|------|------|------|
| React + TypeScript（Vite）| UI構築 | 高速なSPA構築、型安全、保守性高い |
| Tailwind CSS | スタイリング | ユーティリティファーストで効率的に開発 |
| React Hook Form + Yup | フォーム管理・バリデーション | 柔軟で拡張性の高いバリデーションが可能 |
| Zustand | グローバル状態管理 | 軽量かつシンプルに状態を管理 |
| React Router | ページ遷移管理 | ルーティングの柔軟な制御が可能 |
| Recharts | 成長・履歴グラフ表示 | 習慣の視覚化・モチベーション維持に利用 |
| react-slick | 画像比較 | Before/Afterのスライダー表示に使用 |
| @react-google-maps/api | 地図描画 | GoogleMap表示に使用 |

---

## ⚙️ バックエンド

| 技術 | 用途 | 理由 |
|------|------|------|
| Ruby on Rails 7（APIモード） | RESTful API構築 | フルスタック対応、高い生産性 |
| PostgreSQL | データベース | Railsと親和性が高くJSONB対応 |
| Devise + JWT | 認証 | 安全性が高く拡張性もある |
| Active Storage + Cloudinary | 画像保存・CDN配信 | 顔写真や敵画像の管理に利用 |
| Sidekiq + Redis | バックグラウンド処理 | 顔分析API連携の非同期処理 |
| RSpec + FactoryBot | テスト | 信頼性の高いユニット・統合テスト |
| Kaminari | ページネーション | 一覧表示の分割に使用 |

---

## 🧠 AI / 外部API連携

| サービス/API | 用途 | 理由 |
|--------------|------|------|
| Azure Face API | 顔分析 | 髪・眉・肌・目などを高精度にスコアリング |
| Google Maps API | 地図描画 | React上でサロン・クリニック位置を地図に表示するため |
| Google Places API | サロン・クリニック検索 | 推奨理由または検索条件に基づいて最適なサロン・クリニックを取得 |

---

## 🎮 ゲーミフィケーション設計

| 項目 | 内容 |
|------|------|
| 状態管理 | Zustand（敵HP・EXP・ランク・称号） |
| RPGロジック | Rails API（敵生成・ダメージ計算・称号判定） |
| 表示UI | Recharts|
| 永続化 | PostgreSQL（user_enemies / todo_completions 等） |

---

## 🌐 インフラ・CI/CD

| 技術 | 用途 | 理由 |
|------|------|------|
| Docker + Docker Compose | 環境構築 | チームでの再現性ある開発環境 |
| Render / Fly.io | デプロイ | Rails + Reactの構成に最適 |
| GitHub Actions | CI/CD | テスト〜デプロイの自動化対応 |

---

## 🎯 技術スタック（主要構成）

| 層 | 技術 |
|----|------|
| フロントエンド | React（w/ TypeScript）, Vite, React Router, Tailwind CSS, Zustand（状態管理）, React Hook Form + Yup（フォームバリデーション）, @react-google-maps/api（地図描画） |
| バックエンド | Ruby on Rails（APIモード）, PostgreSQL（RDBMS） |
| バックグラウンド処理 | Sidekiq + Redis（非同期処理 / 顔分析API連携など） |
| 顔分析AI | Azure Face API（Microsoft Cognitive Services） |
| 施設検索 | Google Places API（推奨サロン・検索画面双方で使用） |
| CI/CD | GitHub Actions（ビルド・テスト・デプロイ） |
| インフラ | Render または Fly.io（Heroku代替）、Cloudinary（画像CDN） |
| 認証 | Devise（ユーザー認証） + JWTトークン（SPA向け） |
| 地図・位置情報 | Google Maps API（美容サロン/クリニック検索用） |
| その他 | ESLint + Prettier（コード整形）、dotenv（環境変数管理） |

---

## 🧩 アーキテクチャスタイル

- SPA + RESTful API + RDB
- フロントエンドとバックエンドを完全分離
- 非同期処理によるUX最適化

## 🧱 コンポーネント構成

| レイヤー              | 主な責務              | 主な技術                                        |
| ----------------- | ----------------- | ------------------------------------------- |
| Presentation | 画面描画・入力| React, Tailwind CSS              |
| Application | 状態・UIロジック   | Zustand, Custom Hooks                         |
| API       | 業務ロジック      | Ruby on Rails API                           |
| Persistence  | 永続化          | PostgreSQL                                  |
| Integration | 外部連携     | Azure Face API, Google APIs |

## 🔄 データフロー

```text
[新規登録 / ログイン]
React
 ↓
Rails API（/auth/signup, /auth/login）
 ↓
PostgreSQL
 ↓
JWT発行 → Reactで保持
────────────────────────
[顔写真アップロード & 体型入力]
React
 ↓
Rails API（/api/v1/analyses）
 ↓
PostgreSQL（face_analyses / body_analyses）
 ↓
Sidekiq
 ↓
Azure Face API
 ↓
分析結果保存
────────────────────────
[改善ポイント & TODO生成]
Rails
 ↓
face_improvement_points / body_improvement_points
 ↓
todos（source_type=auto）
────────────────────────
[ダッシュボード表示]
Rails API（/api/v1/dashboard）
 ↓
PostgreSQL
 ↓
React（分析結果・TODO・敵・おすすめ表示）
────────────────────────
[TODO完了 → 戦闘処理]
React
 ↓
Rails API（/api/v1/todos/{id}/complete）
 ↓
PostgreSQL
  ├─ todo_completions
  ├─ user_enemies（HP更新）
  ├─ users（EXP / rank）
  └─ user_titles（称号判定）
────────────────────────
[サロン・クリニック推薦]
Rails
 ↓
Google Places API
 ↓
recommendations保存
 ↓
React（Google Maps表示）
```

## 🧰 非機能要件への対応
| 要件           | 対応内容                                 |
| ------------ | ------------------------------------ |
| **セキュリティ**   | JWT認証・HTTPS        |
| **スケーラビリティ** | SPA + API分離 |
| **パフォーマンス**  | Sidekiqによる非同期処理        |
| **運用性**      | CI/CD・Docker |

## 🏗️ アーキテクチャ図（概要）

```text
React
 ↓
Rails API
 ├─ ActiveStorage → Cloudinary
 ├─ Sidekiq → Azure Face API
 └─ Google Places API
 ↓
PostgreSQL
```
--- 


## 画面設計図
https://www.figma.com/design/U7Uw3lRB1mirXUOMFUnaUA/Ikemenize-%E7%94%BB%E9%9D%A2%E8%A8%AD%E8%A8%88%E5%9B%B3?node-id=0-1&p=f&t=RTr1V94lDXyIfPIV-0 


## 画面遷移図
https://boardmix.com/app/editor/WB0nxO0KnXglzz2L0-NgYw


## ユーザーフロー図
https://boardmix.com/app/editor/lDD46G1EajpvbbYgNX8rDg


## ER図
https://app.diagrams.net/#G1CJXn86HYhDUfRt-vPUIvLYZ3iaFWkEIq#%7B%22pageId%22%3A%22e18lIvAGIVrYv3Qxj4Wt%22%7D


## API 仕様書
https://docs.google.com/spreadsheets/d/14gag1ZZ96da0Jm2jU0-V6W6l3y4xm-jO-wHY4mQdBZc/edit?usp=sharing


## テーブル定義書
https://docs.google.com/spreadsheets/d/1X_UtoahqKD2Y_jc2osakkYHBFRnOBUbmHWx94d9NcQQ/edit?usp=sharing