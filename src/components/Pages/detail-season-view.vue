<template>
  <div class="season-details">
    <h1>Temporada: {{ seasonDetails.season_number }}</h1>
    <p><strong>Fecha de estreno:</strong> {{ seasonDetails.air_date }}</p>
    <p>
      {{ seasonDetails.overview || '' }}
    </p>

    <div v-if="seasonDetails.episodes && seasonDetails.episodes.length">
      <h2>Episodios</h2>
      <ul class="episode-list">
        <li
          v-for="episode in seasonDetails.episodes"
          :key="episode.id"
          class="episode-item"
        >
          <h3>Episodio {{ episode.episode_number }}: {{ episode.name }}</h3>
          <p>{{ episode.air_date }}</p>
          <p>
            Sinopsis:
            {{ episode.overview || 'Sin sinopsis disponible' }}
          </p>
          <img :src="getImageUrl(episode.still_path)" alt="Episode image" />
        </li>
      </ul>
    </div>
    <p v-else>No hay episodios disponibles!</p>
  </div>
</template>

<script>
export default {
  name: 'DetailSeason',
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
        'https://api.themoviedb.org/3/tv/4/season/1?language=en-US',
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

<style scoped>
.season-details {
  padding: 20px;
  font-family: Arial, sans-serif;
  color: whitesmoke;
  padding-right: 2%;
  width: 100%;
}

h1,
h2,
h3 {
  margin-bottom: 20px;
}

p {
  margin-bottom: 10px;
}

.episode-list {
  list-style-type: none;
  padding: 0;
}

.episode-item {
  border: 1px solid #232ed1ff;
  padding: 10px;
  margin: 10px 0;
  width: 100%;
  box-sizing: border-box;
}

.episode-item img {
  max-width: 200px;
  display: block;
  margin-top: 10px;
}

@media screen and (min-width: 768px) {
  .episode-item {
    max-width: 100%;
    width: 100%;
  }
}
</style>
