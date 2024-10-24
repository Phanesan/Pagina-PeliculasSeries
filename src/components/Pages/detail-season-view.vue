<template>
  <div class="season-details">
    <h1>Temporada: {{ seasonDetails.season_number }}</h1>
    <p><strong>Fecha de estreno:</strong> {{ seasonDetails.air_date }}</p>
    <p>{{ seasonDetails.overview || '' }}</p>

    <div v-if="seasonDetails.episodes && seasonDetails.episodes.length">
      <h2>Episodios</h2>
      <div class="episode-list">
        <div
          v-for="episode in seasonDetails.episodes"
          :key="episode.id"
          class="episode-item"
        >
          <div class="episode-info">
            <h3>Episodio {{ episode.episode_number }}: {{ episode.name }}</h3>
            <p>{{ episode.air_date }}</p>
            <p>
              Sinopsis:
              {{ episode.overview || 'Sin sinopsis disponible' }}
            </p>
            <img
              v-if="episode.still_path"
              :src="getImageUrl(episode.still_path)"
              alt="Imagen del episodio"
            />
            <img
              v-else
              src="../../assets/img/seriesPlaceholder.png"
              alt="Placeholder"
            />
          </div>
          <div class="guest-stars">
            <h4>Estrellas invitadas:</h4>
            <div class="guest-stars-list">
              <ul>
                <div v-if="episode.guest_stars && episode.guest_stars.length">
                  <div class="guest-stars-container">
                    <div
                      v-for="star in episode.guest_stars"
                      :key="star.id"
                      class="guest-star"
                    >
                      <img
                        v-if="star.profile_path"
                        :src="getImageUrl(star.profile_path)"
                        alt="Imagen de la estrella"
                        @click="
                          $emit('changePage', 'DetailArtist', { id: star.id })
                        "
                      />
                      <img
                        v-else
                        src="../../assets/img/user.png"
                        alt="Usuario"
                      />
                      <p
                        @click="
                          $emit('changePage', 'DetailArtist', { id: star.id })
                        "
                      >
                        {{ star.name }}
                      </p>
                    </div>
                  </div>
                </div>
                <div v-else>No hay estrellas invitadas disponibles.</div>
              </ul>
            </div>
          </div>
        </div>
      </div>
    </div>
    <p v-else>No hay episodios disponibles!</p>
  </div>
</template>

<script>
export default {
  name: 'DetailSeason',
  props: {
    payload: Object,
  },
  data() {
    return {
      seasonDetails: {},
    }
  },
  methods: {
    getImageUrl(path) {
      return path
        ? `https://image.tmdb.org/t/p/w500${path}`
        : 'https://via.placeholder.com/150'
    },
    fetchSeasonDetails() {
      const myHeaders = new Headers()
      myHeaders.append(
        'Authorization',
        'Bearer eyJhbGciOiJIUzI1NiJ9.eyJhdWQiOiI3NTQxODk4NTc3NWNjZTRkMjZjNjc0NTc1ZjRmNDliMiIsIm5iZiI6MTcyOTMxNDc2Ny4zMzYzODQsInN1YiI6IjY3MDc0YjA2ZTQ2YTEyYTE5NDE5ZTg4OCIsInNjb3BlcyI6WyJhcGlfcmVhZCJdLCJ2ZXJzaW9uIjoxfQ.u8RuyookdzmOJ_p30WXq1LP9gnp4b55ups-Ne1Fj-Q4',
      )

      const requestOptions = {
        method: 'GET',
        headers: myHeaders,
        redirect: 'follow',
      }

      fetch(
        `https://api.themoviedb.org/3/tv/${this.payload.id}/season/${this.payload.season}?language=es-MX`,
        requestOptions,
      )
        .then(response => response.json())
        .then(data => {
          console.log(data)
          this.seasonDetails = data
        })
        .catch(error => console.error('Error:', error))
    },
  },
  mounted() {
    this.fetchSeasonDetails()
  },
}
</script>

<style>
.season-details {
  padding: 20px;
  font-family: 'Arial', sans-serif;
  color: whitesmoke;
  width: 100%;
  background-color: #121212;
  border-radius: 10px;
}

h1,
h2,
h3 {
  margin-bottom: 20px;
  font-weight: bold;
  color: #afafaf;
}

p {
  margin-bottom: 10px;
  line-height: 1.6;
}

.episode-list {
  list-style-type: none;
  padding: 0;
}

.episode-item {
  border: 1px solid #232ed1;
  padding: 15px;
  margin: 10px 0;
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 20px;
  box-sizing: border-box;
  background-color: #1e1e1e;
  border-radius: 8px;
}

.episode-item:hover {
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.5);
}

.episode-info {
  padding: 10px;
}

.episode-item img {
  max-width: 100%;
  border-radius: 8px;
}

.guest-stars {
  padding: 10px;
  max-width: 100%;
}

.guest-stars h4 {
  margin-bottom: 10px;
  font-weight: bold;
}

.guest-stars-list {
  display: flex;
  flex-direction: column;
  gap: 10px;
  max-height: 300px;
  overflow-y: auto;
  overflow-x: hidden;
  box-sizing: border-box;
  padding-right: 10px;

  scrollbar-width: thin;
  scrollbar-color: #232ed1 #121212;
}

.guest-stars-list::-webkit-scrollbar {
  width: 20px;
}

.guest-stars-list::-webkit-scrollbar-track {
  background: #121212;
  border-radius: 10px;
}

.guest-stars-list::-webkit-scrollbar-thumb {
  background: #232ed1;
  border-radius: 10px;
}

.guest-stars-list::-webkit-scrollbar-thumb:hover {
  background: #3a3a3a;
}

.guest-star {
  display: flex;
  align-items: center;
  gap: 10px;
  text-align: left;
  cursor: pointer;
  transition: transform 0.3s;
}

.guest-star:hover {
  transform: scale(1.05);
}

.guest-star img {
  max-width: 100px;
  margin-bottom: 10px;
  border-radius: 8px;
  transition: opacity 0.3s;
}

.guest-star img:hover {
  opacity: 0.8;
}

@media screen and (max-width: 768px) {
  .episode-item {
    grid-template-columns: 1fr;
  }
}
</style>
