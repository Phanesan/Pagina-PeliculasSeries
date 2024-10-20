<template>
  <p>Home</p>
  <button @click="$emit('changePage', 'DetailArtist')" class="home-button">Home</button>
  <button @click="$emit('changePage', 'DetailCategory')" class="home-button">Home</button>

</template>

<script>
export default {
  name: 'HomeView',
}
</script>
