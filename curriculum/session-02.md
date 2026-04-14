# 第2回：HonoによるAPI開発の基本

## 1. 今日の目標
- APIの「エンドポイント」の作り方を学ぶ
- パスパラメータ（idなど）とクエリ（検索ワードなど）の受け取り方を理解する

## 2. ルーティングの実装
Honoでは、URLのパターンを簡単に定義できます。

```typescript
// 特定のIDを持つデータを指すパスパラメータ
app.get('/user/:id', (c) => {
  const id = c.req.param('id')
  return c.json({ message: `ユーザーID ${id} の詳細です。` })
})

// 検索などで利用するクエリパラメータ - /search?q=hono
app.get('/search', (c) => {
  const query = c.req.query('q')
  return c.json({ result: `「${query}」の検索結果（仮）` })
})
```

## 3. POSTリクエストとJSON
データを新しく送る（保存する）際の動きを体験します。
```typescript
app.post('/posts', async (c) => {
  // ブラウザ（フロントエンド）から送られてきたJSONを受け取る
  const body = await c.req.json()
  return c.json({
    message: 'データを受け取りました！',
    receivedData: body
  }, 201)
})
```

## 4. ハンズオン課題
**「今日の占いAPI」を作ってみよう**
1. `/fortune` にアクセスすると、ランダムで次のいずれかを返すようにしてください。
   - `{"luck": "大吉"}`
   - `{"luck": "中吉"}`
   - `{"luck": "小吉"}`
2. ブラウザまたは APIクライアント（Talend API Tester や Postman など）で確認してください。

## 5. まとめ
- `c.req.param()`：URLの一部を変数として受け取る。
- `c.req.query()`：?以降のパラメータを受け取る。
- `c.req.json()`：送信されたデータの中身（Body）を受け取る。
