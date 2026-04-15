# 第4回：React 入門（Vite + React）

## 1. 今日の目標
- React の基本概念（コンポーネント、JSX、State）を理解する
- Vite を使って React プロジェクトを作成する
- ボタンを押すとカウントが増えるアプリを作る

## 2. React とは？
React は Facebook（Meta）が開発した **UI構築ライブラリ** です。
画面を「コンポーネント」という部品に分けて作るのが特徴です。

## 3. プロジェクト作成
```bash
npm create vite@latest my-frontend -- --template react-ts
cd my-frontend
npm install
npm run dev
```
ブラウザで `http://localhost:5173` を開くと画面が表示されます。

## 4. JSX とは？
HTML のような見た目だけど、JavaScript の中に書けるのが JSX です。
```tsx
// これは HTML ではなく JSX（JavaScriptの式）
const element = <h1>Hello, world!</h1>
```

### HTML との違い
| HTML | JSX |
|---|---|
| `class="..."` | `className="..."` |
| `for="..."` | `htmlFor="..."` |
| 閉じタグ省略可 | すべて閉じる必要あり (`<br />`) |
| 文字列のみ | `{}` で JavaScript の式を埋め込める |

```tsx
const name = 'Taro'
const element = <h1>Hello, {name}!</h1>
//                        ^^^^^^^^ JS の変数を埋め込んでいる
```

## 5. コンポーネント
関数が JSX を返すと、それがコンポーネントになります。
```tsx
// コンポーネント = 画面の「部品」
function Greeting() {
  return <h1>こんにちは！</h1>
}

// 使うときは HTML タグのように書く
function App() {
  return (
    <div>
      <Greeting />
      <Greeting />
    </div>
  )
}
```

### Props（外から値を渡す）
```tsx
function Greeting({ name }: { name: string }) {
  return <h1>こんにちは、{name}さん！</h1>
}

function App() {
  return (
    <div>
      <Greeting name="Taro" />
      <Greeting name="Hanako" />
    </div>
  )
}
```

## 6. State（状態管理）
画面に表示される値を **動的に変えたい** ときは `useState` を使います。

```tsx
import { useState } from 'react'

function Counter() {
  const [count, setCount] = useState(0)
  //     ^^^^^^ 現在の値
  //             ^^^^^^^^ 値を更新する関数

  return (
    <div>
      <p>カウント: {count}</p>
      <button onClick={() => setCount(count + 1)}>+1</button>
    </div>
  )
}
```

**重要**: `count = count + 1` ではなく `setCount(count + 1)` で更新しないと画面が変わりません。React は `set関数` が呼ばれたときだけ画面を再描画します。

## 7. useEffect（副作用）
コンポーネントの表示後に「何かを実行したい」ときに使います。API通信でよく使います。

```tsx
import { useState, useEffect } from 'react'

function DataLoader() {
  const [message, setMessage] = useState('読み込み中...')

  useEffect(() => {
    // コンポーネント表示後に実行される
    fetch('http://localhost:8787/')
      .then(res => res.text())
      .then(data => setMessage(data))
  }, []) // [] = 最初の1回だけ実行

  return <p>{message}</p>
}
```

## 8. ハンズオン課題
**「シンプルTODOリスト」を作ろう**
1. 入力フォームとボタンを配置する
2. ボタンを押すと、入力内容がリストに追加される
3. リストは `useState` で管理する

ヒント:
```tsx
const [todos, setTodos] = useState<string[]>([])
const [input, setInput] = useState('')

const addTodo = () => {
  setTodos([...todos, input])
  setInput('')
}
```

## 9. まとめ
- **JSX**: HTMLに似た構文を JavaScript 内に書ける。`{}` で式を埋め込む。
- **コンポーネント**: 関数が JSX を返すと UI 部品になる。
- **useState**: 画面に反映される「状態」を管理する。
- **useEffect**: 表示後に実行したい処理（API通信など）を書く。
