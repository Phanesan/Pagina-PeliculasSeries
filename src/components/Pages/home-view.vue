<template>
  <p>Home</p>
  <button @click="$emit('changePage', 'DetailMovie',{ id: 550 })">Detalles movie</button>
</template>

<script>
export default {
  name: 'HomeView',
  props: {
    payload: {
      type: Object
    }
  }
}
</script>
