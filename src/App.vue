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
  <nav id="menu">
    <header>
      <img src="../public/favicon.ico" class="icon" />
      <input
        type="checkbox"
        id="responsive-menu"
        onclick="updatemenu()"
      /><label></label>
      <div class="home" v-if="currentPage !== 'Login'">
        <a @click="goToHome">Home</a>
      </div>
      <ul class="optionsNavBar">
        <li>
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
        </li>
      </ul>
    </header>
  </nav>

  <div class="view">
    <component
      :is="currentPage"
      @changePage="changePage"
      :payload="payload"
      :key="key"
    ></component>
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
      key: 0,
    }
  },
  methods: {
    changePage(page, payload) {
      this.key += 1
      this.payload = payload
      this.currentPage = page
      localStorage.setItem('currentPage', page)
      localStorage.setItem('payloadData', JSON.stringify(payload))
      document.title = "ANX CINEMA"
    },
    logout() {
      localStorage.removeItem('payload')
      localStorage.removeItem('session_id')
      localStorage.removeItem('user')
      localStorage.removeItem('currentPage')
      this.changePage('Login')
    },
    goToHome() {
      this.changePage('Home', {})
    },
  },
  mounted() {
    const token = localStorage.getItem('payload')

    if (token !== null) {
      if(localStorage.getItem('currentPage') !== null && localStorage.getItem('payloadData') !== undefined) {
        this.changePage(localStorage.getItem('currentPage'), localStorage.getItem('payloadData')? JSON.parse(localStorage.getItem('payloadData')) : {})
      } else {
        this.changePage('Login')
      }
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
  max-height: 6rem;
  background-color: #101d42;
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
  padding-top: 3.1rem !important;
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

#menu {
  background: #101d42;
  height: 50px;
  padding-right: 18px;
  border-radius: 10px;
}
#menu ul,
#menu li {
  margin: 0 auto;
  padding: 0;
  list-style: none;
}
#menu ul {
  width: 100%;
  text-align: right;
}
#menu li {
  display: inline-block;
  position: relative;
}
#menu .home {
  background-color: var(--color-primary);
  border-radius: 13px;
  margin-left: 1.5rem;
}
#menu a {
  display: block;
  line-height: 48px;
  padding: 0 14px;
  text-decoration: none;
  color: #ffffff;
  font-size: 16px;
  cursor: pointer;
}
#menu a.dropdown-arrow:after {
  content: '\23F7';
  margin-left: 5px;
}
#menu li a:hover {
  color: #ffffff;
  background: #1a2f6b;
}
#menu input {
  display: none;
  margin: 0;
  padding: 0;
  height: 50px;
  width: 100%;
  opacity: 0;
  cursor: pointer;
}
#menu label {
  display: none;
  line-height: 48px;
  text-align: center;
  position: absolute;
  left: 35px;
}
#menu label:before {
  font-size: 1.6em;
  color: #ffffff;
  content: '\2261';
  margin-left: 20px;
}

.icon {
  max-height: 90px;
}
</style>
