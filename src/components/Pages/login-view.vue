<template>
  <p>Login View</p>
  <button @click="$emit('changePage', 'Home')">Home</button>
</template>

<script>
export default {
  name: 'LoginView'
}
</script>
