# 第6回：Cloudflare D1（データベース）

## 1. 今日の目標
- Cloudflare D1 の概要と「Bindings」の仕組みを理解する
- SQL の基本を学ぶ
- Hono から DB にデータを読み書きする

## 2. D1 とは？
D1 は Cloudflare が提供する **サーバーレスデータベース** です。
SQLite ベースなので、軽量で高速。自分でサーバーを管理する必要がありません。

## 3. Bindings（バインディング）とは？
Cloudflare Workers では、DB や ストレージに直接アクセスすることはできません。
代わりに `wrangler.json` で「このWorkerにはこのDBを使わせる」と **紐付け（Binding）** を行います。

```json
// wrangler.json
{
  "name": "my-app",
  "d1_databases": [
    {
      "binding": "DB",
      "database_name": "my-db",
      "database_id": "ここにIDが入る"
    }
  ]
}
```

`"binding": "DB"` と書くと、コード内で `c.env.DB` としてアクセスできるようになります。

## 4. データベースの作成
```bash
npx wrangler d1 create my-db
```
表示された `database_id` を `wrangler.json` にコピペしてください。

## 5. Hono で Bindings の型を定義する
`c.env.DB` を TypeScript に認識させるため、型を定義します。
```typescript
type Bindings = {
  DB: D1Database
}

const app = new Hono<{ Bindings: Bindings }>()

// これで c.env.DB に型が付き、補完が効くようになる
```

## 6. テーブル定義（SQL入門）
`schema.sql` を作成します。
```sql
CREATE TABLE IF NOT EXISTS messages (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  content TEXT NOT NULL,
  created_at TEXT DEFAULT (datetime('now'))
);
```

ローカルDBに反映します：
```bash
npx wrangler d1 execute my-db --local --file=./schema.sql
```

### SQL 基本チートシート
| 操作 | SQL |
|---|---|
| 全件取得 | `SELECT * FROM messages` |
| 条件付き | `SELECT * FROM messages WHERE id = ?` |
| 挿入 | `INSERT INTO messages (content) VALUES (?)` |
| 削除 | `DELETE FROM messages WHERE id = ?` |
| 件数 | `SELECT COUNT(*) FROM messages` |

## 7. Hono からの DB 操作
```typescript
import { Hono } from 'hono'
import { z } from 'zod'
import { zValidator } from '@hono/zod-validator'

type Bindings = {
  DB: D1Database
}

const app = new Hono<{ Bindings: Bindings }>()

// 全件取得
app.get('/messages', async (c) => {
  const { results } = await c.env.DB
    .prepare('SELECT * FROM messages ORDER BY created_at DESC')
    .all()
  return c.json(results)
})

// 新規投稿
app.post('/messages', zValidator('json', z.object({
  content: z.string().min(1).max(140)
})), async (c) => {
  const { content } = c.req.valid('json')
  await c.env.DB
    .prepare('INSERT INTO messages (content) VALUES (?)')
    .bind(content)
    .run()
  return c.json({ status: 'ok' }, 201)
})

// 削除
app.delete('/messages/:id', async (c) => {
  const id = c.req.param('id')
  await c.env.DB
    .prepare('DELETE FROM messages WHERE id = ?')
    .bind(id)
    .run()
  return c.json({ status: 'deleted' })
})

export default app
```

## 8. ハンズオン
1. 上記の API を実装してください。
2. APIクライアントなどから以下を確認してください：
   - メッセージの投稿（POST）
   - 一覧取得（GET）
   - サーバー再起動後もデータが残っていること

## 9. まとめ
- D1 は SQLite ベースのサーバーレス DB。サーバー管理不要。
- `wrangler.json` で Binding を設定 → `c.env.DB` でアクセス可能に。
- `Hono<{ Bindings: Bindings }>` で型を付けると補完が効く。
- `prepare().bind().all()` / `.run()` でSQLを実行する。
