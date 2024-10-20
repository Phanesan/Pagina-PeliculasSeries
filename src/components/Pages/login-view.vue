<template>
  <p>Login View</p>
  <button @click="$emit('changePage', 'DetailArtist')">Home</button>
  <button @click="$emit('changePage', 'detailCategory')">Category</button>

</template>

<script>
export default {
  name: 'LoginView'
}
</script>
