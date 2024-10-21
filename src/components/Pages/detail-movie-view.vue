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

          <input
            v-model="isFavorite"
            @click="toggleFavorite"
            name="favorite-checkbox"
            id="favorite"
            type="checkbox"
          />
          <label class="containerFavorite" for="favorite">
            <svg
              class="feather feather-heart"
              stroke-linejoin="round"
              stroke-linecap="round"
              stroke-width="2"
              stroke="currentColor"
              fill="none"
              viewBox="0 0 24 24"
              height="24"
              width="24"
              xmlns="http://www.w3.org/2000/svg"
            >
              <path
                d="M20.84 4.61a5.5 5.5 0 0 0-7.78 0L12 5.67l-1.06-1.06a5.5 5.5 0 0 0-7.78 7.78l1.06 1.06L12 21.23l7.78-7.78 1.06-1.06a5.5 5.5 0 0 0 0-7.78z"
              ></path>
            </svg>
            <div class="action">
              <span v-if="!isFavorite">Añadir a favoritos</span>
              <span v-else>Añadido a favoritos</span>
            </div>
          </label>

          <div class="movie-genres">
            <h2>Géneros</h2>
            <span v-for="(genre, index) in movie.genres" :key="genre.id">
              <button
                @click="
                  $emit('changePage', 'DetailCategory',{ id: genre.id })
                "
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
                @click="
                  $emit('changePage', 'DetailKeyword', {
                    keywordId: keyword.id,
                    keywordName: keyword.name,
                  })
                "
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
            <select v-model="rating" id="rating">
              <option disabled value="undefined">Selecciona un rating</option>
              <option v-for="n in 5" :key="n" :value="n">{{ n }}</option>
            </select>
            <br />
            <button class="save-button" @click="saveRating(rating)">
              Guardar rating
            </button>
            <button class="delete-button" @click="deleteRating">
              Eliminar rating
            </button>
          </div>
        </div>
      </div>
    </div>

    <div class="movie-cast">
      <h2>Reparto</h2>
      <div class="scrollable-container">
        <div class="carousel1">
          <div
            class="actor-item"
            v-for="(actor, index) in cast"
            :key="actor.cast_id"
            @click="$emit('changePage', 'DetailArtist', { id: actor.id })"
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
      <div class="scrollable-container">
        <div class="recommended-carousel">
          <div
            class="recommended-item"
            v-for="(recommended, index) in recommendedMovies"
            :key="recommended.id"
            @click="$emit('changePage', 'DetailMovie', { id: recommended.id })"
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
  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'DetailMovieView',
  props: {
    payload: {
      type: Object,
    },
  },
  data() {
    return {
      movie: {},
      cast: [],
      userRating: '',
      keywords: [],
      recommendedMovies: [],
      apiKey: import.meta.env.VITE_API_KEY,
      rating: 'undefined',
      isFavorite: true,
    }
  },
  methods: {
    fetchMovieDetails() {
      const movieId = this.payload.id
      console.log(movieId)
      fetch(
        `https://api.themoviedb.org/3/movie/${movieId}?api_key=${this.apiKey}&language=es-LA`,
      )
        .then(response => response.json())
        .then(data => {
          this.movie = data
          document.title = this.movie.title
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
        `https://api.themoviedb.org/3/movie/${movieId}/credits?api_key=${this.apiKey}&language=es-LA`,
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
        `https://api.themoviedb.org/3/movie/${movieId}/keywords?api_key=${this.apiKey}&language=es-LA`,
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
          `https://api.themoviedb.org/3/movie/${movieId}/videos?api_key=${this.apiKey}`,
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

    fetchAccountState(movieId) {
      fetch(
        `https://api.themoviedb.org/3/movie/${movieId}/account_states?session_id=${localStorage.getItem('session_id')}&api_key=${this.apiKey}`,
      )
        .then(response => response.json())
        .then(data => {
          this.isFavorite = data.favorite
        })
        .catch(error => {
          console.error('Error al obtener el estado de la película: ' + error)
        })
    },
    toggleFavorite() {
      let data = JSON.stringify({
        media_type: 'movie',
        media_id: this.movie.id,
        favorite: this.isFavorite ? false : true,
      })

      let config = {
        method: 'post',
        maxBodyLength: Infinity,
        url: `https://api.themoviedb.org/3/account/${JSON.parse(localStorage.getItem('user')).id}/favorite?session_id=${localStorage.getItem('session_id')}&api_key=${this.apiKey}`,
        headers: {
          'Content-Type': 'application/json',
        },
        data: data,
      }

      axios
        .request(config)
        .then(response => {
          console.log(JSON.stringify(response.data))
        })
        .catch(error => {
          console.log(error)
        })
    },
    async watchTrailer() {
      const trailerKey = await this.fetchMovieTrailer(this.movie.id)

      if (trailerKey) {
        window.open(`https://www.youtube.com/watch?v=${trailerKey}`, '_blank')
      } else {
        alert('No se encontró el tráiler para esta película.')
      }
    },
    saveRating(n) {
      const movieId = this.movie.id
      const sessionId = localStorage.getItem('session_id')
      if (!sessionId) {
        alert('No hay sesión activa. Asegúrate de haber iniciado sesión.')
        return
      }
      const data = JSON.stringify({ value: n })
      console.log(data)
      axios
        .post(
          `https://api.themoviedb.org/3/movie/${movieId}/rating?session_id=${sessionId}&api_key=${this.apiKey}`,
          data,
          {
            headers: {
              'Content-Type': 'application/json',
            },
          },
        )
        .then(response => {
          console.log(response.data)
          alert('Tu calificación ha sido guardada.')
        })
        .catch(error => {
          console.error(error)
        })
    },
    deleteRating() {
      let config = {
        method: 'delete',
        maxBodyLength: Infinity,
        url: `https://api.themoviedb.org/3/movie/${this.movie.id}/rating?session_id=${localStorage.getItem('session_id')}&api_key=${this.apiKey}`,
        headers: {},
      }

      axios
        .request(config)
        .then(response => {
          alert('Tu calificación ha sido borrada.')
          this.rating = 'undefined'
        })
        .catch(error => {
          console.log(error)
        })
    },
    fetchRecommendedMovies(movieId) {
      fetch(
        `https://api.themoviedb.org/3/movie/${movieId}/recommendations?api_key=${this.apiKey}&language=es-LA&page=1`,
      )
        .then(response => response.json())
        .then(data => {
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

    let config = {
      method: 'get',
      maxBodyLength: Infinity,
      url: `https://api.themoviedb.org/3/movie/${this.payload.id}/account_states?session_id=${localStorage.getItem('session_id')}&api_key=${this.apiKey}`,
      headers: {},
    }
    axios
      .request(config)
      .then(response => {
        this.rating = response.data.rated.value
      })
      .catch(error => {
        console.log(error)
      })

    this.fetchAccountState(this.payload.id)
  },
}
</script>

<style>
.containerFavorite {
  background-color: rgb(36, 36, 36);
  display: flex;
  align-items: center;
  gap: 14px;
  padding: 10px 15px 10px 10px;
  cursor: pointer;
  user-select: none;
  border-radius: 10px;
  box-shadow: rgba(46, 46, 46, 0.2) 0px 8px 24px;
  color: rgb(255, 255, 255);
  width: 195px;
}

#favorite {
  display: none;
}

#favorite:checked + .containerFavorite svg {
  fill: hsl(0deg 100% 50%);
  stroke: hsl(0deg 100% 50%);
  animation: heartButton 1s;
}

@keyframes heartButton {
  0% {
    transform: scale(1);
  }

  25% {
    transform: scale(1.3);
  }

  50% {
    transform: scale(1);
  }

  75% {
    transform: scale(1.3);
  }

  100% {
    transform: scale(1);
  }
}

#favorite + .containerFavorite .action {
  position: relative;
  overflow: hidden;
  display: grid;
}

#favorite + .containerFavorite .action span {
  grid-column-start: 1;
  grid-column-end: 1;
  grid-row-start: 1;
  grid-row-end: 1;
  transition: all 0.5s;
}

#favorite + .containerFavorite .action span.option-1 {
  transform: translate(0px, 0%);
  opacity: 1;
}

#favorite:checked + .containerFavorite .action span.option-1 {
  transform: translate(0px, -100%);
  opacity: 0;
}

#favorite + .containerFavorite .action span.option-2 {
  transform: translate(0px, 100%);
  opacity: 0;
}

#favorite:checked + .containerFavorite .action span.option-2 {
  transform: translate(0px, 0%);
  opacity: 1;
}

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
  margin-left: 20px;
  margin-right: 100px;
}

.movie-details {
  display: flex;
  margin-top: 20px;
}

.movie-cast > h2 {
  margin-top: 20px;
  margin-left: 40px;
}

.movie-cast {
  max-width: 100%;
}

.movie-poster {
  width: 400px;
  height: auto;
  margin-right: 20px;
  border-radius: 8px;
}

.movie-info {
  flex-grow: 1;
}

.movie-overview {
  text-align: justify;
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
  margin-top: 15px;
  margin-left: 20px;
}

.carousel-container {
  margin: 30px;
  border-radius: 8px;
  overflow-x: auto;
}

.carousel1 {
  display: flex;
  width: 100vw;
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

.recommended-carousel-container {
  margin-left: 30px;
  margin-right: 30px;
  margin-top: 30px;
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

.scrollable-container {
  display: flex;
  overflow-x: auto;
  gap: 10px;
  position: relative;
  margin-left: 40px;
  margin-right: 40px;
  margin-bottom: 40px;
  border-radius: 8px;
}

.scrollable-container::-webkit-scrollbar {
  height: 12px;
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
