---
title: 'Nuxt3(Vue3) で Google AdSense 広告の貼り方、実装方法'
published: true
publish_date: '2022-04-29T12:52:42+09:00'
updated_date: null
category: 'tech/nuxt3'
tags: [ 'nuxt3:3.0.0-27500677.83d27bd' ]
slug: '01g1sq6443873zfxh44mf46n5e'
metadata:
  description: 'Nuxt3(Nuxt.js) と Vue3(Vue.js) での、Google AdSense 広告の貼り方です。Vue は仮想 DOM を技術ベースとしているため、Google が指定するコードでは動作しません。Vue のお作法に則って実装したサンプルを紹介します。'
thumbnail: null
---


## コードの解説

Nuxt3(Nuxt.js) と Vue3(Vue.js) での、Google AdSense 広告の貼り方です。Vue は仮想 DOM を技術ベースとしているため、Google が指定するコードでは動作しません。Vue のお作法に則って実装したサンプルを紹介します。

```app.vue
<script lang="ts" setup>
// ➊ divコンテナーの参照
const containerRef = ref<HTMLDivElement>();

// ➋ コンポーネントがマウントされたときの処理
onMounted(() => {
  // スクリプトを定義 (AdSense本体をロード)
  const adsScript = document.createElement(`script`);
  adsScript.async = true;
  adsScript.crossOrigin = `anonymous`;
  adsScript.src = `https://pagead2.googlesyndication.com/pagead/js/adsbygoogle.js?client=ca-pub-xxxxxxxxxxxxxxxx`;

  // スクリプトを定義 (AdSense実行)
  const inlineScript = document.createElement(`script`);
  inlineScript.type = `text/javascript`;
  inlineScript.text = `(adsbygoogle = window.adsbygoogle || []).push({});`;

  // DOMに追加
  containerRef.value.appendChild(adsScript);
  containerRef.value.appendChild(inlineScript);
});
</script>

<!-- ➌ Vue Template -->
<template>
  <!-- divコンテナー参照先 -->
  <div ref="containerRef">
    <!-- SSR時にサーバー側で描画しない -->
    <client-only>
      <!-- Google AdSense コード -->
      <ins class="adsbygoogle" style="display:block"
        data-ad-client="ca-pub-xxxxxxxxxxxxxxxx" data-ad-slot="xxxxxxxxxx"
        data-ad-format="auto" data-full-width-responsive="true">
      </ins>
    </client-only>
  </div>
</template>

<style scoped>
/* ➍ 広告未配信なら広告セクションを非表示にする */
ins.adsbygoogle[data-ad-status="unfilled"] {
  display: none !important;
}
</style>
```

## ➊ divコンテナーの参照

```vue
<script lang="ts" setup>
// ➊ divコンテナーの参照
const containerRef = ref<HTMLDivElement>();
</script>

<template>
  <div ref="containerRef"> … </div>
</template>
```

Vue.js の [テンプレート参照機能](https://v3.ja.vuejs.org/guide/component-template-refs.html)を使用することで、template 内を参照できます。script 内の変数名と template 内の ref 名称をそろえる必要があり、ここでは `containerRef` としています。

## ➋ コンポーネントがマウントされたとき

```vue
<script lang="ts" setup>
// ➋ コンポーネントがマウントされたときの処理
onMounted(() => {
  // スクリプトを定義 (AdSenseの外部スクリプト読み込み)
  const adsScript = document.createElement(`script`);
  adsScript.async = true;
  adsScript.crossOrigin = `anonymous`;
  adsScript.src = `https://pagead2.googlesyndication.com/pagead/js/adsbygoogle.js?client=ca-pub-xxxxxxxxxxxxxxxx`;

  // スクリプトを定義 (AdSenseのインナースクリプト実行)
  const inlineScript = document.createElement(`script`);
  inlineScript.type = `text/javascript`;
  inlineScript.text = `(adsbygoogle = window.adsbygoogle || []).push({});`;

  // DOMに追加
  containerRef.value.appendChild(adsScript);
  containerRef.value.appendChild(inlineScript);
});
</script>
```

`onMounted(() => { }` は Vue3 の[ライフサイクルフック](https://v3.ja.vuejs.org/guide/instance.html#%E3%83%A9%E3%82%A4%E3%83%95%E3%82%B5%E3%82%A4%E3%82%AF%E3%83%AB%E3%82%BF%E3%82%99%E3%82%A4%E3%82%A2%E3%82%AF%E3%82%99%E3%83%A9%E3%83%A0)の一つで、コンポーネントがマウントされた後に呼び出されるため、onMounted 内では DOM の操作が可能です。なお、Nuxt.js での SSR(サーバー・サイド・レンダリング) 時はサーバー側では実行されません。

Vue.js では template 内での script 要素が実行できないので、代わりに onMounted 内で AdSense の外部スクリプト読み込みと、AdSense のインナースクリプトを DOM に動的追加しています。

なお、サンプル内の `client=ca-pub-xxxxxxxxxxxxxxxx` は各々の番号に切り替える必要があります。

## ➌ Vue Template

```vue
<!-- ➌ Vue Template -->
<template>
  <!-- divコンテナー参照先 -->
  <div ref="containerRef">
    <!-- SSR時にサーバー側で描画しない -->
    <client-only>
      <!-- Google AdSense コード -->
      <ins class="adsbygoogle" style="display:block"
        data-ad-client="ca-pub-xxxxxxxxxxxxxxxx" data-ad-slot="xxxxxxxxxx"
        data-ad-format="auto" data-full-width-responsive="true">
      </ins>
    </client-only>
  </div>
</template>
```

`<client-only>` は Nuxt.js 専用の要素で、サーバー側では描画されずクライアント側でのみ描画されるようになります。AdSense はクライアント側でのみ描画してほしいので `<client-only>` が必要になります。

Nuxt.js を使用せずに Vue.js 単体の場合は、クライアント側でしか描画しないので `<client-only>` は必要ありません。(というか存在しません～)

Google AdSense が指定してきた ins 要素をここに貼り付けます。
`data-ad-client="ca-pub-xxxxxxxxxxxxxxxx"` と `data-ad-slot="xxxxxxxxxx"` は各々の番号に切り替える必要があります。

## ➍ 広告未配信なら広告セクションを非表示にする

```vue
<style scoped>
/* ➍ 広告未配信なら広告セクションを非表示にする */
ins.adsbygoogle[data-ad-status="unfilled"] {
  display: none !important;
}
</style>
```

作成したばかりのページは広告が配信されないので余計な余白が表示されてしまいます。

未配信の場合 AdSense 側のライブラリが ins 要素に `data-ad-status="unfilled"` 属性を設定します。
この仕組みを利用して CSS で `data-ad-status="unfilled"` 属性が設定されている場合に、AdSense のエリアを画面に表示しない指定をしています。
