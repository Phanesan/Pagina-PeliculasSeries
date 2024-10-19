<template>
  <p>Home</p>
  <button @click="$emit('changePage', 'DetailMovie')">Detalles movie</button>
  <button @click="$emit('changePage', 'DetailKeyword')">Palabras clave</button>
</template>

<script>
export default {
  name: 'HomeView',
}
</script>
