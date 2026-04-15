# Modern Web Development Curriculum (Hono + Cloudflare + Zod + Vite)

このリポジトリは、JavaScriptの基礎を学んだ学生を対象に、現代的なフルスタックWeb開発の手法を習得するための授業資料（全7回 + 特別回）を格納しています。

## 🚀 カリキュラムの概要

現代のWeb開発で主流となりつつある「サーバーレス（エッジ）」と「型安全（TypeScript / Zod）」、そして「高速な開発体験（Vite）」を組み合わせたスタックを学びます。

### 習得できる技術スタック
- **Runtime**: Cloudflare Workers (D1 / Pages)
- **Backend Framework**: [Hono](https://hono.dev/)
- **Validation**: [Zod](https://zod.dev/)
- **Frontend**: [Vite](https://vitejs.dev/) + React
- **ORM/DB**: Cloudflare D1 (SQLite)
- **Language**: TypeScript

## 📚 授業構成

| 回数 | タイトル | 内容 |
| :--- | :--- | :--- |
| 第1回 | モダンJS復習とTypeScript入門 | アロー関数、async/await、型注釈、型推論 |
| 第2回 | Cloudflare Hono入門 | プロジェクト作成、ルーティング、エラーの読み方 |
| 第3回 | Zodによるデータバリデーション | スキーマ定義、zValidator、z.infer |
| 第4回 | React入門 | JSX、コンポーネント、useState、useEffect |
| 第5回 | Hono RPCによるフロント連携 | 型安全な通信、CORS設定 |
| 第6回 | Cloudflare D1（データベース） | Bindings、SQL基礎、CRUD操作 |
| 第7回 | 実践アプリ構築とデプロイ | 統合、DevTools、Cloudflareへの公開 |
| 特別回 | モノレポ構成とコード共有 | npm workspaces、パッケージ分割 |

## 💻 閲覧方法

資料は **Markdown版** と **HTML版** の2つの形式で用意されています。

### HTML版（推奨）
ブラウザで見やすく、コードが色分け（ハイライト）されます。
1. `curriculum/html/index.html` をブラウザで開いてください。

### Markdown版
GitHub上で閲覧したり、自分のエディタ（VS Code等）で直接編集・プレビューしたりする場合に便利です。
1. `curriculum/index.md` を開いてください。

## 🛠 開発環境
- Node.js (v20以上推奨) または Bun
- VS Code (推奨拡張: Prettier, ESLint)
- Cloudflare アカウント (Workers/D1 のデプロイに使用)

## 📄 ライセンス
MIT License
