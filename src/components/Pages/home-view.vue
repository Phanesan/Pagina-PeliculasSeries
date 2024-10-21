<template>
  <p>Home</p>
  <button @click="$emit('changePage', 'DetailArtist')" class="home-button">Login</button>
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
