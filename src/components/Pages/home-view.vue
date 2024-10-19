<template>
  <p>Home</p>
  <button @click="$emit('changePage', 'DetailMovie')">Detalles movie</button>
</template>

<script>
export default {
  name: 'HomeView',
}
</script>
