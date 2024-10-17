<script setup>
import Home from './components/Pages/home-view.vue'
import Login from './components/Pages/login-view.vue'
</script>

<template>
  <header>
    <div class="header-section">
      <img src="/favicon.ico" alt="logo" />
    </div>
    <div class="header-section">
      <p>Peliculas</p>
    </div>
  </header>
  <component :is="currentPage"></component>
</template>

<script>
export default {
  components: {
    Home,
    Login,
  },
  data() {
    return {
      currentPage: 'Login',
    }
  },
  methods: {
    changePage(page) {
      this.currentPage = page
    },
  },
}
</script>

<style>
header {
  display: flex;
  align-items: center;
  justify-content: start;
  padding: 1rem;
  background-color: var(--color-primary);

  .header-section {
    margin-right: 2.5rem;
  }
}
</style>
