<template>
  <p>Login View</p>
  <button @click="$emit('changePage', 'DetailMovie')">Detalles movie</button>
</template>

<script>
export default {
  name: 'LoginView'
}
</script>
