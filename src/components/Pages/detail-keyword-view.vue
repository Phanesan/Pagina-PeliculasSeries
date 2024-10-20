<template>
    <p>Detail Keyword</p>
    <p>{{ this.payload }}</p>
</template>

<script>
export default {
  name: 'DetailKeywordView',
  props: {
    payload: {
      type: Object
    }
  }
}
</script>