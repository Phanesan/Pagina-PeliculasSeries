<script setup>
import axios from 'axios'
</script>

<template>
  <div class="series-details" v-if="seriesData && castData && recommendationsData">
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
          <span
            v-for="(genre, index) in seriesData.genres"
            :key="genre.id"
            @click="$emit('changePage', 'DetailCategory', { id: genre.id })"
          >
            {{ genre.name }}
            <span v-if="index < seriesData.genres.length - 1">, </span>
          </span>
        </p>

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
              class="recommended-image" />
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
            @click="$emit('changePage', 'DetailSeason', { id: seriesData.id, season: index })"
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
              class="season-image" />
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
      apiKey: import.meta.env.VITE_API_KEY,
    }
  },
  methods: {
    async fetchs() {
      await this.fetchSeriesDetails()
      await this.fetchCastSeries()
      await this.fetchRecommendations()
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
    this.fetchs()
  },
}
</script>

<style>
.series-details {
  display: flex;
  flex-direction: column;
  overflow-x: hidden;
  padding: 3rem 3rem;

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
