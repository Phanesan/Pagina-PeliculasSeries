<template>
  <button @click="$emit('changePage', 'Login')" class="home-button">Home</button>

  <div class="artist-details-container">
    <div class="artist-info">
      <div class="left-column">
        <img v-if="actor.profile_path"
          :src="'https://www.themoviedb.org/t/p/w600_and_h900_bestv2/' + actor.profile_path" alt="Actor"
          class="artist-image">
        <p><strong>Información personal</strong></p>

        <p><strong>Conocido por:</strong> Actuando</p>

        <p><strong>Créditos conocidos:</strong> {{ knownCredits }}</p>

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

        <p class="artist-biography">
          {{ isExpanded ? actor.biography : truncatedBiography }}
        </p>

        <button v-if="actor.biography && actor.biography.length > maxBiographyLength" @click="toggleBiography"
          class="toggle-biography-button">
          {{ isExpanded ? 'Ver menos' : 'Ver más' }}
        </button>

        <div class="filter-section">
          <button @click="setFilter('movie')">Películas</button>
          <button @click="setFilter('tv')">Series</button>
        </div>

        <h3>Conocido por:</h3>
        <div class="movies-known-for">
          <div v-for="item in filteredMedia" :key="item.id" class="movie-item">
            <img :src="'https://www.themoviedb.org/t/p/w200/' + item.poster_path" :alt="item.title || item.name"
              class="movie-poster">
            <p>{{ item.title || item.name }}</p>
          </div>
        </div>

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
      isExpanded: false,
      maxBiographyLength: 1000,
      knownCredits:0
    };
  },

  computed: {
    filteredMedia() {
      return this.moviesAndSeries.filter(item => item.media_type === this.filter);
    },
    truncatedBiography() {
      return this.actor.biography
        ? this.actor.biography.substring(0, this.maxBiographyLength) + '...'
        : '';
    },
  },

  methods: {
    getActorDetails() {
      const actorId = new URLSearchParams(window.location.search).get('id');
      fetch(`https://api.themoviedb.org/3/person/10859?api_key=b4014e28c8f91a3d85b70da33bb5afb2`)
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
      fetch(`https://api.themoviedb.org/3/person/10859/combined_credits?api_key=b4014e28c8f91a3d85b70da33bb5afb2`)
        .then(response => response.json())
        .then(data => {
          this.moviesAndSeries = data.cast;
          this.knownCredits = data.cast.length;
        })
        .catch(error => {
          console.error("Error al obtener las películas y series del artista:", error);
        });
    },

    setFilter(type) {
      this.filter = type;
    },

    toggleBiography() {
      this.isExpanded = !this.isExpanded; 
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
    }
  },

  mounted() {
    this.getActorDetails();
  }
}
</script>
<style>


.artist-details-container {
  display: flex;
  flex-direction: column;
  padding: 105px;
  background-color: #2b2b2b;
  color: #e0e0e0;
  font-family: Arial, sans-serif;
}

.artist-info {
  display: flex;
}

.left-column {
  width: 30%;
  display: flex;
  flex-direction: column;
  align-items: center;
  padding-right: 20px;
}

.artist-image {
  width: 70%;
  border-radius: 8px;
}

.left-column p,
.left-column ul {
  text-align: left;
  margin: 10px 0;
}

.right-column {
  width: 70%;
  padding-left: 20px;
  display: flex;
  flex-direction: column;
}

.artist-name {
  font-size: 2em;
  margin-bottom: 20px;
}

.artist-biography {
  line-height: 1.6;
  margin-bottom: 30px;
}

.filter-section {
  margin-bottom: 20px;
}

.filter-section button {
  margin-right: 10px;
  padding: 10px 15px;
  background-color: #563d7c;
  color: white;
  border: none;
  cursor: pointer;
}

.movies-known-for {
  display: flex;
  flex-wrap: wrap;
}

.movie-item {
  width: 150px;
  margin: 10px;
  text-align: center;
}

.movie-poster {
  width: 100%;
  border-radius: 8px;
}

.toggle-biography-button {
  background-color: transparent;
  border: none;
  color: #563d7c;
  cursor: pointer;
  margin-bottom: 20px;
}
</style>
