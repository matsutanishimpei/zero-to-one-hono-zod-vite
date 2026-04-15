# 第5回：Hono RPC によるフロントエンド連携

## 1. 今日の目標
- フロントエンド（React）からバックエンド（Hono）にデータを送受信する
- Hono の RPC 機能で **型安全な通信** を実現する

## 2. 従来の通信方法の問題点
普通に `fetch` で API を呼ぶと、こうなります：
```typescript
// URLは文字列 → タイポしてもエディタが教えてくれない
const res = await fetch('http://localhost:8787/api/users')
const data = await res.json()
// data の型が any → 何が入っているか分からない
```

**RPC を使えば、これらの問題がすべて解消されます。**

## 3. RPC (Remote Procedure Call) とは？
「リモートの関数を呼ぶ」仕組みです。API の URL を文字列で書く代わりに、**バックエンドの関数をそのまま呼ぶような感覚** でコードが書けます。

```typescript
// 従来: URL を直接指定 → タイポのリスク
const res = await fetch('/api/hello', { method: 'POST', body: ... })

// RPC: エディタの補完が効く → タイポ不可能
const res = await client.hello.$post({ json: { name: '学生' } })
```

## 4. バックエンド側の準備
Hono アプリの「型」をエクスポートします。

```typescript
// backend/src/index.ts
import { Hono } from 'hono'
import { zValidator } from '@hono/zod-validator'
import { z } from 'zod'

const app = new Hono()

const route = app.post(
  '/hello',
  zValidator('json', z.object({ name: z.string() })),
  (c) => {
    const { name } = c.req.valid('json')
    return c.json({ message: `こんにちは、${name}さん！` })
  }
)

// ★ この1行が重要！フロントエンドに型情報を渡す
export type AppType = typeof route
export default app
```

## 5. フロントエンド側の実装
```bash
# フロントエンドのプロジェクトで Hono クライアントをインストール
cd my-frontend
npm install hono
```

```tsx
// frontend/src/App.tsx
import { useState } from 'react'
import { hc } from 'hono/client'
import type { AppType } from '../../backend/src/index' // 型だけ借りる

const client = hc<AppType>('http://localhost:8787')

function App() {
  const [name, setName] = useState('')
  const [response, setResponse] = useState('')

  const handleSubmit = async () => {
    // ↓ ここで補完が効く！ .hello も .$post も候補に出る
    const res = await client.hello.$post({ json: { name } })
    const data = await res.json()
    setResponse(data.message) // ← data.message も型が効いている
  }

  return (
    <div>
      <input value={name} onChange={(e) => setName(e.target.value)} />
      <button onClick={handleSubmit}>送信</button>
      <p>{response}</p>
    </div>
  )
}

export default App
```

## 6. CORS 設定
フロントエンド（port 5173）とバックエンド（port 8787）はポートが異なるため、バックエンド側で CORS を許可する必要があります。

```typescript
// backend/src/index.ts
import { cors } from 'hono/cors'

const app = new Hono()
app.use('*', cors()) // すべてのオリジンを許可（開発時のみ）
```

## 7. ハンズオン
1. バックエンド: `/hello` エンドポイントを実装する
2. フロントエンド: 入力フォームから名前を送信し、レスポンスを画面に表示する
3. **補完が効くこと** を確認する（`client.` の後に Ctrl+Space を押してみる）

## 8. まとめ
- `export type AppType = typeof route` でバックエンドの型をエクスポート。
- `hc<AppType>(url)` でフロントエンドから型安全にAPIを呼べる。
- URL のタイプミスやレスポンスの型間違いが **コンパイル時に** 検出される。
- 開発時は `cors()` ミドルウェアを入れないとブラウザがリクエストをブロックする。
