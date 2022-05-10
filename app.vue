<script lang="ts" setup>
// nuxt dev は OK。
// nuxt build でエラーになる…
const contents = import.meta.glob!("./contents/**/*.md");
console.log(`contents >> `, contents);
</script>

<template>
  <div>
    test
  </div>
</template>
