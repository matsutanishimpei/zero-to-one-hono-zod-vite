# 第5回：Cloudflare D1（データベース）

## 1. 今日の目標
- Cloudflareの SQLデータベース「D1」を使う
- データを永続化（ブラウザを閉じても消えないように）する

## 2. データベースの作成
ターミナルから以下のコマンドでデータベースを作成します。
```bash
npx wrangler d1 create my-db
```
作成された `database_id` を `wrangler.json` (または `wrangler.toml`) に貼り付けます。

## 3. テーブル定義 (SQL入門)
`schema.sql` を作成し、データの入れ物（テーブル）を定義します。
```sql
CREATE TABLE messages (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  content TEXT NOT NULL,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```
DBに反映させます：
```bash
npx wrangler d1 execute my-db --local --file=./schema.sql
```

## 4. HonoからのDB操作
```typescript
app.get('/messages', async (c) => {
  // DBから全件取得
  const { results } = await c.env.DB.prepare('SELECT * FROM messages').all()
  return c.json(results)
})

app.post('/messages', zValidator('json', z.object({ content: z.string() })), async (c) => {
  const { content } = c.req.valid('json')
  // データを挿入
  await c.env.DB.prepare('INSERT INTO messages (content) VALUES (?)')
    .bind(content)
    .run()
  return c.json({ status: 'ok' }, 201)
})
```

## 5. ハンズオン
1. 自分のAPIを改造して、メッセージがDBに保存されるようにしましょう。
2. サーバーを再起動しても、データが残っていることを確認してください。

## 6. まとめ
- D1は SQLite ベースのサーバーレスデータベース。
- `prepare(...).all()` や `run()` を使ってSQLを実行する。
- サーバーレスなので、自分でDBサーバーを管理する必要がない。
