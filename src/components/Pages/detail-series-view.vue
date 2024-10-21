<script setup>
import axios from 'axios'
</script>

<template>
  <div
    class="series-details"
    v-if="seriesData && castData && recommendationsData && keywords"
  >
    <div class="series-top">
      <div class="banner-series">
        <img
          v-if="seriesData.poster_path"
          class="series-poster"
          :src="`https://www.themoviedb.org/t/p/w600_and_h900_bestv2/${seriesData.poster_path}`"
          alt="serie poster"
        />
        <img
          v-else
          src="../../assets/img/seriesPlaceholder.png"
          class="series-poster"
          alt="serie poster"
        />
      </div>

      <div class="series-info">
        <h1>{{ seriesData.name }}</h1>
        <p>
          <strong>Lanzamiento 📅:</strong>
          {{ seriesData.first_air_date }}
        </p>
        <p>
          <strong>Idioma Original 🗺️:</strong>
          {{ getLanguageFullName(seriesData.original_language) }}
        </p>

        <p>{{ seriesData.overview }}</p>

        <p>
          <strong>Generos: </strong>
          <div class="genre-container-series"></div>
          <span
            v-for="(genre, index) in seriesData.genres"
            :key="genre.id"
            class="genre-button"
            @click="$emit('changePage', 'DetailCategory', { id: genre.id })"
          >
            {{ genre.name }}
          </span>
        </p>

        <p>
          <strong>Palabras clave: </strong>
          <div class="keywords-list">
            <span
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
            </span>
          </div>
        </p>

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

        <p>
          <strong>Popularidad: </strong>
          {{ seriesData.popularity }}
        </p>
        <p>
          <strong>Calificación: </strong>
          {{ seriesData.vote_average }} / 10
        </p>
        <p>
          <strong>Votos: </strong>
          {{ seriesData.vote_count }}
        </p>

        <p>
          <strong>Idiomas: </strong>
          <span
            v-for="(language, index) in seriesData.spoken_languages"
            :key="language.english_name"
          >
            {{ language.english_name }}
            <span v-if="index < seriesData.spoken_languages.length - 1"
              >,
            </span>
          </span>
        </p>

        <p>
          <strong>Temporadas: </strong>
          {{ seriesData.number_of_seasons }}
        </p>

        <p>
          <strong>Episodios: </strong>
          {{ seriesData.number_of_episodes }}
        </p>

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

    <div class="series-bottom">
      <div class="series-reparto">
        <h2>Reparto</h2>
        <div class="scrollable-container">
          <div
            class="actor-item"
            v-for="actor in castData.cast"
            :key="actor.id"
            @click="$emit('changePage', 'DetailArtist', { id: actor.id })"
          >
            <img
              v-if="actor.profile_path"
              :src="`https://www.themoviedb.org/t/p/w185${actor.profile_path}`"
              alt="actor profile"
              class="actor-image"
            />
            <img v-else src="../../assets/img/user.png" class="actor-image" />
            <p>{{ actor.name }}</p>
            <p>como {{ actor.character }}</p>
          </div>
        </div>
      </div>

      <div class="series-recommended">
        <h2>Series Recomendadas</h2>
        <div class="scrollable-container">
          <div
            class="recommended-item"
            v-for="recommended in recommendationsData.results"
            :key="recommended.id"
            @click="$emit('changePage', 'DetailSeries', { id: recommended.id })"
          >
            <img
              v-if="recommended.poster_path"
              :src="`https://www.themoviedb.org/t/p/w185${recommended.poster_path}`"
              alt="Poster de {{ recommended.name }}"
              class="recommended-image"
            />
            <img
              v-else
              src="../../assets/img/seriesPlaceholder.png"
              class="recommended-image"
            />
            <p>
              <strong>{{ recommended.name }}</strong>
            </p>
          </div>
        </div>
      </div>

      <div class="series-seasons">
        <h2>Temporadas</h2>
        <div class="scrollable-container">
          <div
            class="season-item"
            v-for="(season, index) in seriesData.seasons"
            :key="season.id"
            @click="
              $emit('changePage', 'DetailSeason', {
                id: seriesData.id,
                season: index,
              })
            "
          >
            <img
              v-if="season.poster_path"
              :src="`https://www.themoviedb.org/t/p/w185${season.poster_path}`"
              alt="Poster de {{ season.name }}"
              class="season-image"
            />
            <img
              v-else
              src="../../assets/img/seriesPlaceholder.png"
              class="season-image"
            />
            <p>
              <strong>{{ season.name }}</strong>
            </p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'DetailSeriesView',
  props: {
    payload: {
      type: Object,
    },
  },
  data() {
    return {
      seriesData: undefined,
      castData: undefined,
      recommendationsData: undefined,
      keywords: undefined,
      apiKey: import.meta.env.VITE_API_KEY,
      isFavorite: false,
      rating: 'undefined',
    }
  },
  methods: {
    async fetchs() {
      await this.fetchSeriesDetails()
      await this.fetchCastSeries()
      await this.fetchRecommendations()
      await this.fetchKeywords()
      await this.fetchAccountState(this.payload.id)
      await this.fetchDataUserForSeries()
    },
    async fetchKeywords() {
      let config = {
        method: 'get',
        maxBodyLength: Infinity,
        url: `https://api.themoviedb.org/3/tv/${this.payload.id}/keywords?api_key=${this.apiKey}`,
        headers: {},
      }

      axios
        .request(config)
        .then(response => {
          this.keywords = response.data.results
        })
        .catch(error => {
          console.log(error)
        })
    },
    async fetchRecommendations() {
      let config = {
        method: 'get',
        maxBodyLength: Infinity,
        url: `https://api.themoviedb.org/3/tv/${this.payload.id}/recommendations?language=es-MX&page=1&api_key=${this.apiKey}`,
        headers: {},
      }

      axios
        .request(config)
        .then(response => {
          this.recommendationsData = response.data
        })
        .catch(error => {
          console.log(error)
        })
    },
    async fetchCastSeries() {
      try {
        let config = {
          method: 'get',
          maxBodyLength: Infinity,
          url: `https://api.themoviedb.org/3/tv/${this.payload.id}/credits?language=es-MX&api_key=${this.apiKey}`,
          headers: {},
        }

        axios
          .request(config)
          .then(response => {
            this.castData = response.data
          })
          .catch(error => {
            console.log(error)
          })
      } catch (error) {
        console.error('Error al obtener los actores de la serie: ' + error)
      }
    },
    async fetchSeriesDetails() {
      try {
        let config = {
          method: 'get',
          maxBodyLength: Infinity,
          url: `https://api.themoviedb.org/3/tv/${this.payload.id}?language=es-MX&api_key=${this.apiKey}`,
          headers: {},
        }

        axios
          .request(config)
          .then(response => {
            this.seriesData = response.data
            console.log(this.payload.id)
            console.log(response.data)
          })
          .catch(error => {
            console.log(error)
          })
      } catch (error) {
        console.error('Error al obtener los datos de la serie: ' + error)
      }
    },
    async fetchAccountState(seriesId) {
      fetch(
        `https://api.themoviedb.org/3/tv/${seriesId}/account_states?session_id=${localStorage.getItem('session_id')}&api_key=${this.apiKey}`,
      )
        .then(response => response.json())
        .then(data => {
          this.isFavorite = data.favorite
        })
        .catch(error => {
          console.error('Error al obtener el estado de la película: ' + error)
        })
    },
    async fetchMovieTrailer(serieId) {
      try {
        const response = await fetch(
          `https://api.themoviedb.org/3/tv/${serieId}/videos?api_key=${this.apiKey}`,
        )
        const data = await response.json()
        const trailer = data.results.find(video => video.site === 'YouTube')

        if (trailer) {
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
      const trailerKey = await this.fetchMovieTrailer(this.seriesData.id)

      if (trailerKey) {
        window.open(`https://www.youtube.com/watch?v=${trailerKey}`, '_blank')
      } else {
        alert('No se encontró el tráiler para esta película.')
      }
    },
    saveRating(n) {
      const data = JSON.stringify({ value: n })
      console.log(data)
      axios
        .post(
          `https://api.themoviedb.org/3/tv/${this.seriesData.id}/rating?session_id=${localStorage.getItem('session_id')}&api_key=${this.apiKey}`,
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
        url: `https://api.themoviedb.org/3/tv/${this.seriesData.id}/rating?session_id=${localStorage.getItem('session_id')}&api_key=${this.apiKey}`,
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
    async fetchDataUserForSeries() {
      let config = {
        method: 'get',
        maxBodyLength: Infinity,
        url: `https://api.themoviedb.org/3/tv/${this.payload.id}/account_states?session_id=${localStorage.getItem('session_id')}&api_key=${this.apiKey}`,
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
    async toggleFavorite() {
      let data = JSON.stringify({
        media_type: 'tv',
        media_id: this.seriesData.id,
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
  },
  mounted() {
    this.fetchs()
  },
}
</script>

<style>
.genre-container-series {
  display: flex;
  flex-wrap: wrap;
  justify-content: flex-start;
  padding-top: 0.6rem;
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

.series-details {
  display: flex;
  flex-direction: column;
  overflow-x: hidden;
  padding: 3rem 3rem;

  .containerFavorite {
    margin-bottom: 1rem;
  }

  .series-top {
    display: flex;
    flex-direction: row;
    margin-bottom: 1.6rem;

    .banner-series {
      display: flex;
      align-items: center;
      justify-content: center;

      .series-poster {
        width: 32rem;
        height: 100%;
        margin-right: 1.4rem;
        border-radius: 8px;
      }
    }
  }

  .series-bottom {
    display: flex;
    flex-direction: column;

    .scrollable-container {
      display: flex;
      margin: 0px;
      overflow-x: auto;
      gap: 10px;
      max-width: 100%;
    }

    .series-reparto {
      display: flex;
      flex-direction: column;
      align-items: start;
      margin-bottom: 2.7rem;

      .actor-item {
        margin-right: 10px;
        background-color: var(--color-secondary);
      }
    }

    .series-recommended {
      display: flex;
      flex-direction: column;
      align-items: start;
      margin-bottom: 2.7rem;

      .recommended-item {
        margin-right: 10px;
        background-color: var(--color-secondary);
        border-radius: 8px;

        img {
          border-top-left-radius: 8px;
          border-top-right-radius: 8px;
        }
      }
    }

    .series-seasons {
      display: flex;
      flex-direction: column;
      align-items: start;
      margin-bottom: 2.7rem;

      .season-item {
        margin-right: 10px;
        background-color: var(--color-secondary);
        border-radius: 8px;
        cursor: pointer;

        p {
          text-align: center;
        }

        img {
          border-top-left-radius: 8px;
          border-top-right-radius: 8px;
          width: 180px;
          height: 255px;
        }
      }
    }
  }
}
</style>
