# 第3回：Zod によるデータバリデーション

## 1. 今日の目標
- 「バリデーション」の重要性を理解する
- Zod を使ってデータの「形」を定義する
- Hono と連携して、不正なリクエストを自動で弾く

## 2. なぜバリデーションが必要か？
APIは誰でもリクエストを送れます。悪意のあるユーザーが以下のような不正データを送ってきたらどうなるでしょう？

```json
{ "name": "", "age": -100, "email": "これはメールじゃない" }
```

チェックなしでDBに保存すると、アプリが壊れたり、セキュリティホールになったりします。
**サーバー側でのチェック（バリデーション）は必須** です。

## 3. Zod のインストール
```bash
npm install zod @hono/zod-validator
```

## 4. スキーマの定義
```typescript
import { z } from 'zod'

// データの「正しい形」を定義する
const userSchema = z.object({
  name: z.string().min(1, '名前は必須です').max(20, '名前は20文字以内です'),
  age: z.number().int('整数で入力してください').positive('正の数で入力してください'),
  email: z.string().email('正しいメールアドレスを入力してください')
})
```

### よく使う Zod のメソッド
| メソッド | 意味 |
|---|---|
| `z.string()` | 文字列 |
| `z.number()` | 数値 |
| `z.boolean()` | true / false |
| `.min(n)` | 最小値 or 最小文字数 |
| `.max(n)` | 最大値 or 最大文字数 |
| `.email()` | メールアドレス形式か |
| `.optional()` | 省略可能にする |

## 5. TypeScript との連携
Zod のスキーマから TypeScript の型を **自動生成** できます。二重管理が不要です。
```typescript
// スキーマから型を生成
type User = z.infer<typeof userSchema>
// ↑ { name: string; age: number; email: string } と同じ
```

## 6. Hono との連携 (zValidator)
`@hono/zod-validator` を使うと、リクエストのチェックを1行で追加できます。
```typescript
import { zValidator } from '@hono/zod-validator'

app.post('/register', zValidator('json', userSchema), (c) => {
  // ここに到達 = バリデーション通過済みの安全なデータ
  const data = c.req.valid('json')
  return c.json({ message: `${data.name}さん、登録完了！` })
})
```

バリデーションに失敗すると、自動的に **400エラー** とエラー詳細がレスポンスされます。

## 7. ハンズオン
1. 上記のコードを実装してください。
2. APIクライアント（ブラウザの DevTools > Console で `fetch()` を使ってもOK）から、わざと不正なデータを送ってみましょう。
   - `age` に `-1` を入れる
   - `email` を `"abc"` にする
   - `name` を空文字にする
3. どんなエラーメッセージが返ってくるか確認してください。

## 8. まとめ
- `z.object({...})` でデータのルール（スキーマ）を定義する。
- `z.infer<typeof schema>` でスキーマから型を自動生成できる。
- `zValidator` を挟むだけで、不正なデータを自動で弾いてくれる。
