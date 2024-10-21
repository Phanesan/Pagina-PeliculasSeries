<template>
  <div id="container">
    <h1 id="keyword-title">{{ keywordName }}</h1>

    <h2 class="section-title">Películas</h2>
    <div class="scrollable-container">
      <div id="movies-carousel" class="carousel">
        <div v-if="movies.length === 0" class="no-results">
          <p>No se encontraron películas.</p>
        </div>
        <div
          class="card"
          v-for="movie in movies"
          :key="movie.id"
          @click="viewMovie(movie)"
        >
          <img
            :src="`https://www.themoviedb.org/t/p/w600_and_h900_bestv2/${movie.poster_path}`"
            alt="movie poster"
          />
          <h2>{{ movie.title }}</h2>
          <p>
            <strong>📅:</strong>
            {{ movie.release_date }}
          </p>
          <p>
            <strong>🗺️:</strong>
            {{ getLanguageFullName(movie.original_language) }}
          </p>
        </div>
      </div>
    </div>

    <h2 class="section-title">Series</h2>
    <div id="series-carousel" class="carousel">
      <div v-if="series.length === 0" class="no-results">
        <p>No se encontraron series.</p>
      </div>
      <div
        class="card"
        v-for="serie in series"
        :key="serie.id"
        @click="viewSerie(serie)"
      >
        <img
          :src="`https://www.themoviedb.org/t/p/w600_and_h900_bestv2/${serie.poster_path}`"
          alt="serie poster"
        />
        <h2>{{ serie.name }}</h2>
        <p>
          <strong>📅:</strong>
          {{ serie.first_air_date }}
        </p>
        <p>
          <strong>🗺️:</strong>
          {{ getLanguageFullName(serie.original_language) }}
        </p>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'DetailKeywordView',
  props: {
    payload: {
      type: Object,
      required: true,
    },
  },
  data() {
    return {
      movies: [],
      series: [],
      keywordName: this.payload.keywordName,
    }
  },
  methods: {
    getMovies() {
      const keywordId = this.payload.keywordId
      fetch(
        `https://api.themoviedb.org/3/discover/movie?api_key=13c164db7b0cbbc91a51acf2fcc65f79&with_keywords=${keywordId}`,
      )
        .then(response => response.json())
        .then(data => {
          this.movies = data.results
        })
        .catch(error => {
          console.error('Error: ' + error)
        })
    },
    getSeries() {
      const keywordId = this.payload.keywordId
      fetch(
        `https://api.themoviedb.org/3/discover/tv?api_key=13c164db7b0cbbc91a51acf2fcc65f79&with_keywords=${keywordId}`,
      )
        .then(response => response.json())
        .then(data => {
          this.series = data.results
        })
        .catch(error => {
          console.error('Error: ' + error)
        })
    },
    viewMovie(movie) {
      this.$emit('changePage', 'DetailMovie', {
        id: movie.id,
        movieTitle: movie.title,
      })
    },
    viewSerie(serie) {
      const serieId = serie.id
      window.location.href = `serie.html?id=${serieId}`
    },
    getLanguageFullName(abbr) {
      const languageMap = {
        en: 'Inglés',
        es: 'Español',
        fr: 'Francés',
        de: 'Alemán',
        it: 'Italiano',
        ja: 'Japonés',
        ko: 'Coreano',
        pt: 'Portugués',
        zh: 'Chino',
        ru: 'Ruso',
      }
      return languageMap[abbr] || abbr
    },
  },
  mounted() {
    this.getMovies()
    this.getSeries()
  },
}
</script>

<style>
#container {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 20px;
}

#keyword-title {
  font-size: 32px;
  color: #fff;
  margin-bottom: 20px;
  text-align: center;
}

.section-title {
  font-size: 28px;
  color: #fff;
  margin-top: 40px;
  margin-bottom: 10px;
  text-align: left;
  width: 100%;
  padding-left: 20px;
}

.carousel {
  display: flex;
  overflow-x: auto;
  gap: 20px;
  padding: 20px;
  width: 100%;
  max-width: 1200px;
  scroll-behavior: smooth;
}

.card {
  min-width: 250px;
  max-width: 300px;
  background-color: #f0f0f0;
  border-radius: 10px;
  overflow: hidden;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  cursor: pointer;
  transition: transform 0.2s ease;
  display: flex;
  flex-direction: column;
  height: 600px;
}

.card:hover {
  transform: scale(1.05);
}

.card img {
  width: 100%;
  height: 80%;
  object-fit: fill;
}

.card h2 {
  font-size: 18px;
  margin: 10px 0;
  text-align: center;
}

.card p {
  padding: 0 10px;
  font-size: 14px;
  color: #555;
  text-align: justify;
  overflow: hidden;
  text-overflow: ellipsis;
}

.no-results {
  color: #fff;
  text-align: center;
  font-size: 18px;
  margin: 20px 0;
  width: 100%;
}

.scrollable-container {
  display: flex;
  overflow-x: auto;
  gap: 10px;
  position: relative;
  margin-left: 40px;
  margin-right: 40px;
  border-radius: 8px;
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
  background-color: #8888886e;
  border-radius: 4px;
}

.scrollable-container::-webkit-scrollbar-thumb:hover {
  background-color: var(--oxford-blue);
}
</style>
