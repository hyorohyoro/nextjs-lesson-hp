# fetch

```fetch
import fetch from "node-fetch";
const apiUrl = "https://jsonplaceholder.typicode.com/posts";

export async function getAllPostsData() {
  const res = await fetch(new URL(apiUrl));
  const posts = await res.json();
  return posts;
}
```

# getStaticProps

- 必ず ServerSide で実行
- page 内でのみ使用可能
- npm start で.next->server フォルダ内でデータ取得後の HTML が生成されているのが確認できる

```getStaticProps
//コンポーネント内でpropsでビルド時に取得したデータを取得する
const Blog = ({posts})=>{}

// データを取得して　propsに渡す
export async function getStaticProps(){
  const posts = await getAllPostsData();
  return { props: { posts }}
}
```

# Dynamic routes

- Next ではディレクトリ構成とファイル名がルーティングのパスに対応する

`ex: pages/posts/[id].js`

- [id]の部分をページによって動的に変更される

- 流れ
  - getStaticPaths() -> id の一覧を取得(どういった ID があるのか確認)
  - getStaticProps() -> 各 id を使って個別データを取得
  - 取得したデータを props でコンポーネントに渡して pre-rendering

```params必須
// 全Idを取得して一覧を生成 その時のキー名は「params:」
export async function getAllPostIds() {
  const res = await fetch(new URL(apiUrl));
  const posts = await res.json();

  return posts.map((post) => {
    return {
      params: {
        id: String(post.id),
      },
    };
  });
}
```

```個別データ取得
// URLを動的に生成
export async function getPostData(id) {
  const res = await fetch(new URL(`${apiUrl}/${id}`));
  const post = await res.json();

  return {
    post,
  };
}
```
