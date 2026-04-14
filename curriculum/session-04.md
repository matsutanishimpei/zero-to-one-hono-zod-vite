# 第4回：Vite + Hono RPCによるフロントエンド連携

## 1. 今日の目標
- フロントエンド（Vite + React）を作成する
- Honoの最強機能「RPC」を使って、バックエンドと型安全に通信する

## 2. RPC (Remote Procedure Call) とは？
通常、APIを呼ぶときは `fetch('/api/user/1')` のように文字列でURLを書きます。
RPCを使えば、`client.user.$get({ param: { id: '1' } })` のように、まるで**バックエンドの関数をそのまま呼んでいるような感覚**でコードが書けます。

## 3. フロントエンドのセットアップ
プロジェクトのルートで以下を実行します。
```bash
npm create vite@latest frontend -- --template react-ts
cd frontend
npm install
# Honoのクライアントライブラリをインストール
npm install hono
```

## 4. 連携の設定
バックエンド（Hono）で定義した「アプリの型」をフロントエンドで共有します。

**Backend (my-app/src/index.ts)**:
```typescript
const route = app.post('/hello', ...)
export type AppType = typeof route
```

**Frontend (frontend/src/App.tsx)**:
```typescript
import { hc } from 'hono/client'
import type { AppType } from '../../my-app/src/index' // 型だけ借りる

const client = hc<AppType>('http://localhost:8787')

// 通信の実装
const fetchHello = async () => {
  const res = await client.hello.$post({ json: { name: '学生' } })
  const data = await res.json()
  console.log(data.message)
}
```

## 5. ハンズオン
1. サーバーから取得したメッセージを、Reactの `useState` を使って画面に表示させてみましょう。
2. 入力フォームを作って、入力した名前をサーバーに送ってみましょう。

## 6. まとめ
- バックエンドの `AppType` を共有することで、フロントエンドで補完が効くようになる。
- APIのパスが変わっても、コード上でエラーが出るのでバグにすぐ気づける。
