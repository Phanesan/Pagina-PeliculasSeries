<template>
    <div class="artist-details-container">
      <button @click="$emit('changePage', 'Home')" class="home-button">Home</button>
  
      <div class="artist-info">
        <div class="left-column">
          <img v-if="actor.profile_path" :src="'https://www.themoviedb.org/t/p/w600_and_h900_bestv2/' + actor.profile_path" alt="Actor" class="artist-image">
          <p><strong>Información personal</strong></p>
          <p><strong>Conocido por:</strong> Actuando</p>
          <p><strong>Créditos conocidos:</strong> {{ actor.known_for_department || 'Desconocido' }}</p>
          <p><strong>Sexo:</strong> {{ actor.gender === 1 ? 'Mujer' : 'Hombre' }}</p>
          <p><strong>Cumpleaños:</strong> {{ actor.birthday ? formatBirthday(actor.birthday) : 'Desconocido' }}</p>
          <p><strong>Lugar de Nacimiento:</strong> {{ actor.place_of_birth || 'Desconocido' }}</p>
          <p><strong>También Conocido Como:</strong></p>
          <ul>
            <li v-for="name in actor.also_known_as" :key="name">{{ name }}</li>
          </ul>
        </div>
  
        <div class="right-column">
          <h1 class="artist-name">{{ actor.name }}</h1>
          <p class="artist-biography">{{ actor.biography }}</p>
  
          <div class="filter-section">
            <button @click="setFilter('movie')">Películas</button>
            <button @click="setFilter('tv')">Series</button>
          </div>
  
          <h3>Conocido por:</h3>
          <div class="movies-known-for">
            <div v-for="item in filteredMedia" :key="item.id" class="movie-item">
              <img :src="'https://www.themoviedb.org/t/p/w200/' + item.poster_path" :alt="item.title || item.name" class="movie-poster">
              <p>{{ item.title || item.name }}</p>
            </div>
          </div>
  
          <button @click="volver" class="back-button">Volver</button>
        </div>
      </div>
    </div>
  </template>
  
  
  <script>
export default {
  name: 'DetailArtistView',

  data() {
    return {
      actor: {},
      moviesAndSeries: [], 
      filter: 'movie', 
    };
  },
  computed: {
    filteredMedia() {
      return this.moviesAndSeries.filter(item => item.media_type === this.filter);
    }
  },
  methods: {
    getActorDetails() {
      const actorId = new URLSearchParams(window.location.search).get('id');
      fetch(`https://api.themoviedb.org/3/person/73421?api_key=b4014e28c8f91a3d85b70da33bb5afb2`)
        .then(response => response.json())
        .then(data => {
          this.actor = data;
          this.getActorMoviesAndSeries(actorId);
        })
        .catch(error => {
          console.error("Error al obtener los detalles del artista:", error);
        });
    },

    getActorMoviesAndSeries(actorId) {
      fetch(`https://api.themoviedb.org/3/person/73421/combined_credits?api_key=b4014e28c8f91a3d85b70da33bb5afb2`)
        .then(response => response.json())
        .then(data => {
          this.moviesAndSeries = data.cast; 
        })
        .catch(error => {
          console.error("Error al obtener las películas y series del artista:", error);
        });
    },

    setFilter(type) {
      this.filter = type; 
    },

    formatBirthday(birthday) {
      const options = { year: 'numeric', month: 'long', day: 'numeric' };
      const date = new Date(birthday);
      return `${date.toLocaleDateString('es-ES', options)} (${this.calculateAge(date)} años)`;
    },

    calculateAge(birthday) {
      const ageDiffMs = Date.now() - new Date(birthday).getTime();
      const ageDate = new Date(ageDiffMs);
      return Math.abs(ageDate.getUTCFullYear() - 1970);
    },

    volver() {
      window.history.back();
    }
  },
  mounted() {
    this.getActorDetails();
  }
}
</script>