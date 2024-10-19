<template>
  <div id="movieDetails">
    <button class="back-button" @click="$emit('changePage', 'Home')">
      ⟵ Regresar
    </button>

    <div class="movie-content">
      <div class="movie-details">
        <img
          class="movie-poster"
          :src="`https://www.themoviedb.org/t/p/w600_and_h900_bestv2/${movie.poster_path}`"
          alt="movie poster"
        />
        <div class="movie-info">
          <h1 class="movie-title">{{ movie.title }}</h1>
          <h3 class="movie-tagline">{{ movie.tagline }}</h3>

          <div class="movie-meta">
            <div>
              <h3>Lanzamiento:</h3>
              <span>{{ movie.release_date }}</span>
            </div>
            <div>
              <h3>Lenguaje original:</h3>
              <span>{{ getLanguageFullName(movie.original_language) }}</span>
            </div>
            <div>
              <h3>Popularidad:</h3>
              <span>{{ movie.popularity }}</span>
            </div>
            <div>
              <h3>Votos:</h3>
              <span>{{ movie.vote_count }}</span>
            </div>
            <div>
              <h3>Calificación:</h3>
              <span>{{ movie.vote_average }} / 10</span>
            </div>
          </div>

          <button class="trailer-button" @click="watchTrailer">
            🎬 Ver Tráiler
          </button>

          <div class="movie-genres">
            <h2>Géneros</h2>
            <span v-for="(genre, index) in movie.genres" :key="genre.id">
              <button
                @click="redirectToCategory(genre.id)"
                class="genre-button"
              >
                {{ genre.name }}
              </button>
            </span>
          </div>

          <div class="movie-keywords">
            <h2>Palabras clave</h2>
            <div class="keywords-list">
              <button
                v-for="(keyword, index) in keywords"
                :key="keyword.id"
                class="keyword"
                @click="$emit('changePage', 'DetailKeyword', { keywordId: keyword.id, keywordName: keyword.name })"
              >
                {{ keyword.name }}
              </button>
            </div>
          </div>

          <div class="movie-overview">
            <h2>Sinopsis</h2>
            <span>{{ movie.overview }}</span>
          </div>

          <div class="rating">
            <h2 for="rating">Tu calificación:</h2>
            <select v-model="userRating" id="rating">
              <option disabled value="">Selecciona un rating</option>
              <option v-for="n in 5" :key="n" :value="n">{{ n }}</option>
            </select>
            <br />
            <button class="save-button" @click="saveRating">
              Guardar calificación
            </button>
            <button class="delete-button" @click="deleteRating">
              Eliminar calificación
            </button>
          </div>
        </div>
      </div>
    </div>

    <div class="movie-cast">
      <h2>Reparto</h2>
      <div class="carousel-container">
        <div class="carousel">
          <div
            class="actor-item"
            v-for="(actor, index) in cast"
            :key="actor.cast_id"
          >
            <img
              v-if="actor.profile_path"
              :src="`https://www.themoviedb.org/t/p/w185${actor.profile_path}`"
              alt="Foto de {{ actor.name }}"
              class="actor-image"
            />
            <img v-else src="../../assets/img/user.png" class="actor-image" />
            <p>
              <strong>{{ actor.name }}</strong>
            </p>
            <p>como {{ actor.character }}</p>
          </div>
        </div>
      </div>
    </div>

    <div class="recommended-movies" v-if="recommendedMovies.length > 0">
      <h2>Películas Recomendadas</h2>
      <div class="recommended-carousel-container">
        <div class="recommended-carousel">
          <div
            class="recommended-item"
            v-for="(recommended, index) in recommendedMovies"
            :key="recommended.id"
          >
            <img
              v-if="recommended.poster_path"
              :src="`https://www.themoviedb.org/t/p/w185${recommended.poster_path}`"
              alt="Poster de {{ recommended.title }}"
              class="recommended-image"
            />
            <p>
              <strong>{{ recommended.title }}</strong>
            </p>
          </div>
        </div>
      </div>
    </div>
    <div v-else>
      <p>No hay películas recomendadas disponibles.</p>
    </div>
  </div>
</template>

<script>
export default {
  name: 'DetailMovieView',
  props: {
    payload: {
      type: Object
    }
  },
  data() {
    return {
      movie: {},
      cast: [],
      userRating: '',
      keywords: [],
      recommendedMovies: [],
    }
  },
  methods: {
    fetchMovieDetails() {
      const movieId = this.payload.id
      fetch(
        `https://api.themoviedb.org/3/movie/${movieId}?api_key=13c164db7b0cbbc91a51acf2fcc65f79`,
      )
        .then(response => response.json())
        .then(data => {
          this.movie = data
          document.title = this.movie.title
          this.loadRating(movieId)
          this.fetchMovieCast(movieId)
          this.fetchMovieKeywords(movieId)
          this.fetchRecommendedMovies(movieId)
        })
        .catch(error => {
          console.error(
            'Error al obtener los detalles de la película: ' + error,
          )
        })
    },
    fetchMovieCast(movieId) {
      fetch(
        `https://api.themoviedb.org/3/movie/${movieId}/credits?api_key=13c164db7b0cbbc91a51acf2fcc65f79`,
      )
        .then(response => response.json())
        .then(data => {
          this.cast = data.cast
        })
        .catch(error => {
          console.error('Error al obtener el reparto de la película: ' + error)
        })
    },
    fetchMovieKeywords(movieId) {
      fetch(
        `https://api.themoviedb.org/3/movie/${movieId}/keywords?api_key=13c164db7b0cbbc91a51acf2fcc65f79`,
      )
        .then(response => response.json())
        .then(data => {
          this.keywords = data.keywords
        })
        .catch(error => {
          console.error('Error al obtener las palabras clave: ' + error)
        })
    },
    async fetchMovieTrailer(movieId) {
      try {
        const response = await fetch(
          `https://api.themoviedb.org/3/movie/${movieId}/videos?api_key=13c164db7b0cbbc91a51acf2fcc65f79`,
        )
        const data = await response.json()
        const trailer = data.results.find(video => video.site === 'YouTube')

        if (trailer) {
          console.log('Tráiler encontrado:', trailer)
          return trailer.key
        } else {
          console.error(
            'No se encontró un tráiler o teaser en YouTube para esta película.',
          )
          alert('No se encontró un tráiler o teaser para esta película.')
          return null
        }
      } catch (error) {
        console.error('Error al obtener el tráiler: ' + error)
        return null
      }
    },
    async watchTrailer() {
      const trailerKey = await this.fetchMovieTrailer(this.movie.id)

      if (trailerKey) {
        window.open(`https://www.youtube.com/watch?v=${trailerKey}`, '_blank')
      } else {
        alert('No se encontró el tráiler para esta película.')
      }
    },
    goBack() {
      window.history.back()
    },
    saveRating() {
      if (this.userRating) {
        const movieId = this.movie.id
        localStorage.setItem(`rating_${movieId}`, this.userRating)
        alert(`Gracias por calificar con ${this.userRating} estrellas.`)
      } else {
        alert('Por favor selecciona un rating antes de guardar.')
      }
    },
    loadRating(movieId) {
      const savedRating = localStorage.getItem(`rating_${movieId}`)
      if (savedRating) {
        this.userRating = savedRating
      }
    },
    deleteRating() {
      const movieId = this.movie.id
      localStorage.removeItem(`rating_${movieId}`)
      this.userRating = ''
      alert('Tu calificación ha sido eliminada.')
    },
    redirectToCategory(genreId) {
      this.$emit('changePage', 'DetailCategory', { genreId })
    },
    fetchRecommendedMovies(movieId) {
      fetch(
        `https://api.themoviedb.org/3/movie/${movieId}/recommendations?api_key=13c164db7b0cbbc91a51acf2fcc65f79&language=es-US&page=1`,
      )
        .then(response => response.json())
        .then(data => {
          console.log('Películas recomendadas:', data.results)
          this.recommendedMovies = data.results
        })
        .catch(error => {
          console.error('Error al obtener películas recomendadas: ' + error)
        })
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
    this.fetchMovieDetails()
  },
}
</script>

<style>
body {
  margin: 0;
  padding: 0;
}

.view {
  max-width: 100%;
}

#movieDetails {
  overflow-x: hidden;
}

.movie-content {
  display: flex;
  flex-wrap: wrap;
  justify-content: flex-start;
}

.movie-details {
  display: flex;
  margin-top: 20px;
}

.movie-cast > h2 {
  margin-top: 20px;
  margin-left: 40px;
}

.movie-poster {
  width: 400px;
  height: auto;
  margin-right: 20px;
}

.movie-info {
  flex-grow: 1;
}

.movie-title {
  font-size: 28px;
  margin: 0;
}

.movie-tagline {
  font-style: italic;
  color: #986ff8;
}

.trailer-button {
  background-color: #8a1336;
  color: white;
  padding: 8px 12px;
  margin: 15px 0;
  border: none;
  cursor: pointer;
  border-radius: 8px;
}

.genre-button {
  background-color: #fbbf24;
  color: white;
  padding: 8px 10px;
  margin: 5px 3px;
  border: none;
  cursor: pointer;
  font-size: 0.9rem;
  border-radius: 8px;
}

.keywords-list {
  display: flex;
  flex-wrap: wrap;
}

.keyword {
  background-color: #4ade80;
  color: rgb(39, 39, 39);
  padding: 8px 12px;
  margin: 5px;
  border: none;
  cursor: pointer;
  border-radius: 8px;
}

.save-button {
  background-color: #4cad59;
  color: white;
  padding: 5px 10px;
  border: none;
  cursor: pointer;
  border-radius: 8px;
  margin-right: 8px;
  margin-top: 8px;
  margin-bottom: 8px;
}

.delete-button {
  background-color: #ef4444;
  color: white;
  padding: 5px 10px;
  border: none;
  cursor: pointer;
  border-radius: 8px;
  margin-right: 8px;
  margin-top: 15px;
  margin-bottom: 8px;
}

.back-button {
  background-color: #f87171;
  color: white;
  padding: 8px 10px;
  border: none;
  cursor: pointer;
  border-radius: 8px;
  margin-bottom: 5px;
  margin-top: 10px;
}

.carousel-container {
  margin: 30px;
  border-radius: 8px;
  overflow-x: auto;
}

.carousel {
  display: flex;
}

.actor-item {
  text-align: center;
  margin-right: 10px;
  background-color: #272727;
  border-radius: 8px;
  cursor: pointer;
}

.actor-image {
  width: 150px;
  height: 200px;
  border-radius: 8px;
}

.actor-name {
  font-weight: bold;
}

.recommended-carousel-container {
  margin: 30px;
  border-radius: 8px;
  overflow-x: auto;
}

.recommended-carousel {
  display: flex;
}

.recommended-item {
  text-align: center;
  margin-right: 10px;
  cursor: pointer;
}

.recommended-image {
  width: 150px;
  height: 225px;
  border-radius: 8px;
}

.recommended-movies > h2 {
  margin-top: 20px;
  margin-left: 40px;
}

select {
  width: 150px;
  border-radius: 5%;
  margin-right: 8px;
  margin-top: 8px;
  margin-bottom: 8px;
  color: var(--color-text);
  background-color: #272727;
}
</style>
