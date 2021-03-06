+++
thumbnail = "images/jsonplaceholder/title.png"
tags = ["JSONPlaceholder","API"]
categories = ["技術メモ"]
date = "2019-02-06T00:00:00+09:00"
title = "JSONPlaceholderを試してみた"
description = ""
+++

JSONPlaceholderというものを知ったので試しに少し使ってみました。

## JSONPlaceholderとは

https://jsonplaceholder.typicode.com/

RESTで実装されたAPIサーバーです。  
APIサーバと言っても、返してくるのはダミーのデータ、POSTしても実際にデータが登録される訳ではなくスタブのようなもので、あくまでもテストなどに便利に使える位置付けのサービスです。

具体的なユースケースは以下の通り。

- curlコマンドのオプションを確認するため何でも良いからAPIを叩きたい
- APIにアクセスするプログラムの学習
- PostmanなどAPIツールのお試しに

テストや学習に使いどころはアイデア次第で色々あるでしょう。

## APIの仕様

用意されてるリソースは６種類で下記のような階層構造になっています。

```
/users
   └/posts
   │   └/comments
   └/albums
   │   └/photos
   └/todos
```

データ取得は`GET`、追加は`POST`、更新は`PUT`、削除は`DELETE`で、いわゆる一般的なRESTのAPIとしてアクセスできます。
部分的に更新したい場合は`PATCH`だそうです。初めて知った。
親子関係にあるデータは`posts/1/commets`のように、階層的にも取得できます。

## サンプル実装

### GET

サンプルとして、JavaScriptのFetch APIでpostsのid=1取得してみます。

```js
fetch("https://jsonplaceholder.typicode.com/posts/1")
    .then(res => res.json())
    .then(json => console.log(json))
    .catch(e => console.error(e.message));
```

こんな感じでダミーデータが返却されます。何語なんでしょうね。

```js
Object {
  body: "quia et suscipit
suscipit recusandae consequuntur expedita et cum
reprehenderit molestiae ut ut quas totam
nostrum rerum est autem sunt rem eveniet architecto",
  id: 1,
  title: "sunt aut facere repellat provident occaecati excepturi optio reprehenderit",
  userId: 1
}
```

### POST

今度はPOSTでデータ追加してみます。

```js
let param = {
    method: "POST",
    body: JSON.stringify({
        title: "a1 test",
        body: "this is test by a1",
        userId: 1
    }),
    headers: {
        "Content-type": "application/json; charset=UTF-8"
    }
};
fetch("https://jsonplaceholder.typicode.com/posts/", param)
    .then(res => res.json())
    .then(json => {
      console.log("post", json);
      fetch(`https://jsonplaceholder.typicode.com/posts/${json.id}`)
        .then(res => res.json())
        .then(json => console.log("query", json))
        .catch(e => console.error(e.message));
    })
    .catch(e => console.error(e.message));
```

1つ目のJSONがPOSTの戻り値で登録したデータが返却されてきます。ただし、直後に登録したidでGETしてもデータは帰ってきません。あくまでもダミーのAPIですので実際には登録はされません。

```js
"post" Object {
  body: "this is test by a1",
  id: 101,
  title: "a1 test",
  userId: 1
}
"query" Object {}
```


## PUT

あまりPOSTと変わりません。methdが`PUT`になって、URLが`post/1`に変わるぐらい。

```js
let param = {
    method: "PUT",
    body: JSON.stringify({
        title: "a1 update test",
        body: "updated by a1",
        userId: 1,
        id: 1
    }),
    headers: {
        "Content-type": "application/json; charset=UTF-8"
    }
};
fetch("https://jsonplaceholder.typicode.com/posts/1", param)
    .then(res => res.json())
    .then(json => {
        console.log("put", json);
        fetch(`https://jsonplaceholder.typicode.com/posts/${json.id}`)
            .then(res => res.json())
            .then(json => console.log("query", json))
            .catch(e => console.error(e.message));
    })
    .catch(e => console.error(e.message));
```

同じようにPUTの戻りには更新した情報が帰ってくるが、実際に変更された訳ではない。

```js
"put" Object {
  body: "updated by a1",
  id: 1,
  title: "a1 update test",
  userId: 1
}
"query" Object {
  body: "quia et suscipit
suscipit recusandae consequuntur expedita et cum
reprehenderit molestiae ut ut quas totam
nostrum rerum est autem sunt rem eveniet architecto",
  id: 1,
  title: "sunt aut facere repellat provident occaecati excepturi optio reprehenderit",
  userId: 1
}
```

PATCHは似たような感じなので省略。

## DELETE

DELETEも実際にデータが消える訳ではなくフェイク。

```js
let param = {
    method: "DELETE"
};
fetch("https://jsonplaceholder.typicode.com/posts/1", param)
    .then(res => res.json())
    .then(json => {
        console.log("del", json);
    })
    .catch(e => console.error(e.message));
```

## My JSON Server

https://my-json-server.typicode.com/

同じ人が作っているサービスで、自分で用意したJSONデータを返すようにできます。  
使い方はすごい簡単でGitHubにリポジトリを作成してdb.jsonと言う名前でファイルを置くだけです。

{{% img src="images/jsonplaceholder/howto.png" w="800" h="187" %}}

ちなみにFreeプランでは下記の制限があって、大量データなどは使えないようですが課金すれば少し上限は上がるようです。

```
10KB db.json max
5 max REST endpoints
30 max items per endpoint
```

### やってみた

GitHubにリポジトリを追加して適当なdb.jsonファイルを置いてみました。

{{% img src="images/jsonplaceholder/json.png" w="800" h="520" %}}

それだけで、下記のURLからAPIにアクセスできます。

https://my-json-server.typicode.com/eichisanden/myJSONServer

{{% img src="images/jsonplaceholder/myjsonserver.png" w="800" h="520" %}}

```json
fetch("https://my-json-server.typicode.com/eichisanden/myJSONServer/food/1")
    .then(res => res.json())
    .then(json => {
        console.log(json);
    })
    .catch(e => console.error(e.message));
```

```js
Object {
  id: 1,
  name: "寿司"
}
```

自分でデータを配置できるので学習とかテスト以外にも応用できそうです。
現時点では認証がないので、配置するデータはご注意ください。

## おわりに

RESTのAPIとはどんなものって人が試してみたり、APIにアクセスするコーディングの学習には良いサービスでした。
