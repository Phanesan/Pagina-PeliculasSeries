<template>
  <div class="artist-details-container">
    <div class="artist-info">
      <div class="left-column">
        <img v-if="actor.profile_path"
          :src="'https://www.themoviedb.org/t/p/w600_and_h900_bestv2/' + actor.profile_path" alt="Actor"
          class="artist-image">
        <img v-else src="../../assets/img/user.png" class="actor-image" />
        <p><strong>Información personal</strong></p>

        <p><strong>Conocido por:</strong> <br> Actuando</p>

        <p><strong>Créditos conocidos:</strong> <br> {{ knownCredits }}</p>

        <p><strong>Sexo:</strong> <br> {{ actor.gender === 1 ? 'Mujer' : 'Hombre' }}</p>

        <p><strong>Cumpleaños:</strong> <br> {{ actor.birthday ? formatBirthday(actor.birthday) : 'Desconocido' }}</p>

        <p><strong>Lugar de Nacimiento:</strong> <br> {{ actor.place_of_birth || 'Desconocido' }}</p>

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
          <div class="scrollable-container">
            <div v-for="item in filteredMedia" :key="item.id" class="movie-item"
            @click="$emit('changePage', item.media_type === 'tv' ? 'DetailSeries' : 'DetailMovie', { id: item.id })">
              <img  v-if="item.poster_path" 
              :src="'https://www.themoviedb.org/t/p/w200/' + item.poster_path" :alt="item.title || item.name"
                class="movie-poster"
                @click="$emit('changePage', item.media_type === 'tv' ? 'DetailSeries' : 'DetailMovie', { id: item.id })">
                <img v-else src="../../assets/img/movie.png" class="poster-image" />
              <p @click="$emit('changePage', item.media_type === 'tv' ? 'DetailSeries' : 'DetailMovie', { id: item.id })">{{ item.title || item.name }}</p>
            </div>
          </div>
        </div>

        <h3 class="timeline-title">Películas y papeles que ha interpretado:</h3>
        <div class="timeline">
          <div class="timeline-item" v-for="item in moviesAndSeries" :key="item.id">
            <div class="timeline-date">{{ item.release_date || 'Sin fecha' }}</div>
            <div class="timeline-content">
              <a @click="$emit('changePage', item.media_type === 'tv' ? 'DetailSeries' : 'DetailMovie', { id: item.id })">{{ item.title || item.name }}</a>
              <p>Papel: {{ item.character || 'Desconocido' }}</p>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>

export default {
  name: 'DetailArtistView',
  props: {
    payload: {
      type: Object
    }
  },
  data() {
    return {
      actor: {},
      moviesAndSeries: [],
      filter: 'movie',
      isExpanded: false,
      maxBiographyLength: 1000,
      knownCredits: 0
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
      const actorId = this.payload.id;
      fetch(`https://api.themoviedb.org/3/person/${actorId}?api_key=b4014e28c8f91a3d85b70da33bb5afb2`)
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
      fetch(`https://api.themoviedb.org/3/person/${actorId}/combined_credits?api_key=b4014e28c8f91a3d85b70da33bb5afb2`)
        .then(response => response.json())
        .then(data => {
          this.moviesAndSeries = data.cast.sort((a, b) => new Date(b.release_date || b.first_air_date) - new Date(a.release_date || a.first_air_date));

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
    },
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
  margin-left: 130px;
  padding: 55px;
  background-color: var(--rich-black);
  color: #e0e0e0;
  font-family: Arial, sans-serif;
  overflow-x: hidden;

  
.artist-info {
  display: flex;
}

.left-column {
  width: 20%;
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  padding: 20px;
  background-color: var(--oxford-blue);
  border-radius: 8px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  color: rgb(255, 255, 255);
}

.artist-image {
  width: 100%;
  border-radius: 8px;
}


.left-column p,
.left-column ul {
  margin: 10px 0;
  font-size: 1em;
}

.left-column strong {
  font-weight: bold;
  color: #fffcfc;
}


.right-column {
  width: 70%;
  padding-left: 20px;
  display: flex;
  flex-direction: column;
}

.artist-name {
  font-size: 2.5em;
  margin-bottom: 20px;
  font-weight: 600;
  text-shadow: 2px 3px 4px rgba(2, 2, 2, 0.5);
}

.artist-biography {
  line-height: 1.6;
  margin-bottom: 30px;
  background-color: var(--oxford-blue);
  color: white;
  padding: 15px;
  border-radius: 8px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

.filter-section {
  padding-left: 380px;
  margin-bottom: 20px;
}

.filter-section button {
  margin-right: 10px;
  padding: 10px 15px;
  background-color: var(--oxford-blue);
  color: white;
  border-radius: 8px;
  border: none;
  cursor: pointer;
}

.movies-known-for {
  display: flex;
  flex-wrap: wrap;
}

.toggle-biography-button {
  background-color: transparent;
  border: none;
  color: white;
  cursor: pointer;
  margin-bottom: 20px;
}

.movie-item {
  flex: 0 0 auto;
  text-align: center;
  width: 150px;
}

.movie-poster {
  width: 100%;
  height: auto;
  border-radius: 8px;
}

.scrollable-container {
  display: flex;
  overflow-x: auto;
  gap: 10px;

  position: relative;
}

.scrollable-container::-webkit-scrollbar {
  height: 12px;
  margin-bottom: -50px;
}

.scrollable-container::-webkit-scrollbar-track {
  background-color: #e0e0e0;
  border-radius: 6px;
}

.scrollable-container::-webkit-scrollbar-thumb {
  background-color: #0000006e;
  border-radius: 4px;
}

.scrollable-container::-webkit-scrollbar-thumb:hover {
  background-color: var(--oxford-blue);
}

.actor-image {
  width: 270px;
  height: 300px;
}

.poster-image {
  border-radius: 8px;
  width: 150px;
  height: 225px;
}


.timeline {
  display: flex;
  flex-direction: column;
  position: relative;
  padding-left: 40px;
  margin-top: 20px;
}

.timeline::before {
  content: '';
  position: absolute;
  left: 20px;
  top: 0;
  bottom: 0;
  width: 2px;
  background: #ffffff; 
}

.timeline-item {
  margin-bottom: 20px;
  position: relative;
}

.timeline-item::before {
  content: '';
  position: absolute;
  left: -10px;
  top: 0;
  width: 10px;
  height: 10px;
  background-color: #007bff; 
  border-radius: 50%;
}

.timeline-date {
  padding-left: 6px;
  padding-bottom: 4px;
  font-weight: bold;
  color: #007bff;
}

.timeline-content {
  margin-left: 20px;
  background-color: #1a1a2e; 
  padding: 15px;
  border-radius: 8px;
  color: #ffffff;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.2); 
  border: 1px solid #007bff; 
}

.timeline-content a {
  color: #ffcc00; 
  text-decoration: none;
  cursor: default;
}

.timeline-content a:hover {
  text-decoration: underline;
  cursor: pointer;
}

.timeline-content p {
  margin-top: 5px;
  font-style: italic;
}

.timeline-title {
  font-weight: bold;
  padding-top: 30px;
}
}


</style>