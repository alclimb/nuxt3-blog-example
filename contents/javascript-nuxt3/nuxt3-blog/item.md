---
title: 'Nuxt3 で .md ファイル(マークダウン)を取得する方法 (import.meta.glob)'
published: true
publish_date: '2021-11-06T15:17:00+09:00'
updated_date: null
category: 'tech/nuxt3'
tags: [ 'nuxt3:3.0.0-27306529.1ef6e3f' ]
slug: '01fm14vfw10c43ebpz49s0954r'
metadata:
  description: ''
---


## プロジェクトの作成

```shell
# nuxt3の新規プロジェクトを作成
> npx nuxi init my-blog-nuxt3-md

# カレントディレクトリを移動
> cd my-blog-nuxt3-md

# 各種パッケージのインストール
> npm i

# デバッグサーバーで実行
> npm run dev
```

## 記事一覧を取得する方法

```ts
const modules = import.meta.glob!("../contents/**/*.md");
console.log(modules);
```

```json
{
  '../contents/tech/nuxt3/item.md': [Function: ../contents/tech/nuxt3/item.md],
  '../contents/tech/git/01.change-git-default-branch-name/item.md': [Function: ../contents/tech/git/01.change-git-default-branch-name/item.md]
}
```

https://github.com/vitejs/vite/issues/77

`import.meta.glob` を使う方法は `nuxi build` で以下のエラーが発生するので使えなかった。

```
> npm run build

@ build D:\src\web-blog\2021-11-06 my-blog-nuxt3-md
nuxi build

Nuxt CLI v3.0.0-27277498.850ef69
i Vite warmed up in 800ms

 ERROR  [rollup-plugin-dynamic-import-variables] Assigning to rvalue (1:2)
file: D:/src/web-blog/2021-11-06 my-blog-nuxt3-md/public/contents/tech/nuxt3/item.md:1:2
```