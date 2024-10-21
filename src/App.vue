<script setup>
import Home from './components/Pages/home-view.vue'
import Login from './components/Pages/login-view.vue'
import DetailMovie from './components/Pages/detail-movie-view.vue'
import DetailSeason from './components/Pages/detail-season-view.vue'
import DetailSeries from './components/Pages/detail-series-view.vue'
import DetailKeyword from './components/Pages/detail-keyword-view.vue'
import DetailArtist from './components/Pages/detail-artist-view.vue'
import DetailCategory from './components/Pages/detail-category-view.vue'
</script>

<template>
  <header>
    <div class="header-section">
      <img src="/favicon.ico" alt="logo" />
    </div>
    <div class="header-section">
      <p>Peliculas</p>
    </div>
    <div class="logout-section" v-if="currentPage !== 'Login'">
      <button class="Btn" @click="logout">
        <div class="sign">
          <svg viewBox="0 0 512 512">
            <path
              d="M377.9 105.9L500.7 228.7c7.2 7.2 11.3 17.1 11.3 27.3s-4.1 20.1-11.3 27.3L377.9 406.1c-6.4 6.4-15 9.9-24 9.9c-18.7 0-33.9-15.2-33.9-33.9l0-62.1-128 0c-17.7 0-32-14.3-32-32l0-64c0-17.7 14.3-32 32-32l128 0 0-62.1c0-18.7 15.2-33.9 33.9-33.9c9 0 17.6 3.6 24 9.9zM160 96L96 96c-17.7 0-32 14.3-32 32l0 256c0 17.7 14.3 32 32 32l64 0c17.7 0 32 14.3 32 32s-14.3 32-32 32l-64 0c-53 0-96-43-96-96L0 128C0 75 43 32 96 32l64 0c17.7 0 32 14.3 32 32s-14.3 32-32 32z"
            ></path>
          </svg>
        </div>
        <div class="text">Cerrar Sesión</div>
      </button>
    </div>
  </header>
  <div class="view">
    <component :is="currentPage" @changePage="changePage" :payload="payload" :key="key"></component>
  </div>
</template>

<script>
export default {
  components: {
    Home,
    Login,
    DetailMovie,
    DetailSeason,
    DetailSeries,
    DetailKeyword,
    DetailArtist,
    DetailCategory,
  },
  data() {
    return {
      payload: null,
      currentPage: 'Login',
      key: 0
    }
  },
  methods: {
    changePage(page, payload) {
      this.key += 1
      this.payload = payload
      this.currentPage = page
    },
    logout() {
      localStorage.removeItem('payload')
      localStorage.removeItem('session_id')
      this.changePage('Login')
    },
  },
  mounted() {
    const token = localStorage.getItem('payload')

    if (token !== null) {
      this.changePage('Home')
    }
  },
}
</script>

<style scooped>
header {
  display: flex;
  align-items: center;
  justify-content: start;
  padding: 1rem;
  max-height: 4.5rem;
  background-color: var(--color-primary);
  width: 100%;
  position: fixed;
  z-index: 1000;


  .header-section {
    margin-right: 2.5rem;

    p {
      color: var(--color-text);
      font-size: 1.8rem;
    }
  }

  .logout-section {
    display: flex;
    width: 100%;
    justify-content: flex-end;
  }
}

.view {
  padding-top: 4.28rem !important;
}

.Btn {
  --black: #000000;
  --ch-black: #141414;
  --eer-black: #1b1b1b;
  --night-rider: #2e2e2e;
  --white: #ffffff;
  --af-white: #f3f3f3;
  --ch-white: #e1e1e1;
  display: flex;
  align-items: center;
  justify-content: flex-start;
  width: 45px;
  height: 45px;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  position: relative;
  overflow: hidden;
  transition-duration: 0.3s;
  box-shadow: 2px 2px 10px rgba(0, 0, 0, 0.199);
  background-color: var(--af-white);
}

.sign {
  width: 100%;
  transition-duration: 0.3s;
  display: flex;
  align-items: center;
  justify-content: center;
}

.sign svg {
  width: 17px;
}

.sign svg path {
  fill: var(--night-rider);
}

.text {
  position: absolute;
  right: 0%;
  width: 0%;
  opacity: 0;
  color: var(--night-rider);
  font-size: 1.2em;
  font-weight: 600;
  transition-duration: 0.3s;
}

.Btn:hover {
  width: 125px;
  border-radius: 5px;
  transition-duration: 0.3s;
}

.Btn:hover .sign {
  width: 30%;
  transition-duration: 0.3s;
  padding-left: 20px;
}

.Btn:hover .text {
  opacity: 1;
  width: 70%;
  transition-duration: 0.3s;
  padding-right: 10px;
}

.Btn:active {
  transform: translate(2px, 2px);
}
</style>
