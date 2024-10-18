<template>
  <div id="movieDetails">
    <button class="back-button" @click="goBack">⟵ Regresar</button>

    <div class="movie-content">
      <img
        class="movie-poster"
        :src="
          'https://www.themoviedb.org/t/p/w600_and_h900_bestv2/' +
          movie.poster_path
        "
        alt="movie poster"
      />
      <div class="movie-info">
        <h1 class="movie-title">{{ movie.title }}</h1>
        <p class="movie-tagline">{{ movie.tagline }}</p>

        <div class="movie-meta">
          <p>
            <strong>Lanzamiento:</strong> <span>{{ movie.release_date }}</span>
          </p>
          <p>
            <strong>Lenguaje original:</strong>
            <span>{{ movie.original_language }}</span>
          </p>
          <p>
            <strong>Popularidad:</strong> <span>{{ movie.popularity }}</span>
          </p>
          <p>
            <strong>Votos:</strong> <span>{{ movie.vote_count }}</span>
          </p>
          <p>
            <strong>Calificación:</strong>
            <span>{{ movie.vote_average }} / 10</span>
          </p>
        </div>

        <button class="trailer-button" @click="watchTrailer">
          🎬 Ver Tráiler
        </button>

        <p class="movie-genres">
          <strong>Géneros:</strong>
          <span v-for="(genre, index) in movie.genres" :key="genre.id">
            <button @click="redirectToCategory(genre.id)" class="genre-button">
              {{ genre.name }}
            </button>
            <span v-if="index < movie.genres.length - 1">, </span>
          </span>
        </p>

        <div class="movie-keywords">
          <h3>Palabras clave</h3>
          <div class="keywords-list">
            <button
              v-for="(keyword, index) in keywords"
              :key="keyword.id"
              class="keyword"
            >
              {{ keyword.name }}
            </button>
          </div>
        </div>

        <p class="movie-overview">
          <strong>Sinopsis:</strong> <span>{{ movie.overview }}</span>
        </p>

        <div class="rating">
          <label for="rating">Tu calificación:</label>
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
              alt="Foto de {{ actor.name }} "
              class="actor-image"
            />
            <p>
              <strong>{{ actor.name }}</strong>
            </p>
            <p>como {{ actor.character }}</p>
          </div>
        </div>
      </div>
    </div>
  </div>

  <button class="artist-button" @click="$emit('changePage', 'DetailArtist')">
    Ver Detalle del Artista
  </button>
</template>

<script>
export default {
  name: 'DetailMovieView',
  data() {
    return {
      movie: {},
      cast: [],
      userRating: '',
      keywords: [], // Nueva variable para almacenar las palabras clave
    }
  },
  methods: {
    fetchMovieDetails() {
      const movieId = 117 // Usa el ID de la película
      fetch(
        `https://api.themoviedb.org/3/movie/${movieId}?api_key=13c164db7b0cbbc91a51acf2fcc65f79`,
      )
        .then(response => response.json())
        .then(data => {
          this.movie = data
          document.title = this.movie.title
          this.loadRating(movieId)
          this.fetchMovieCast(movieId)
          this.fetchMovieKeywords(movieId) // Llamada para obtener las palabras clave
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
    fetchMovieTrailer(movieId) {
      fetch(
        `https://api.themoviedb.org/3/movie/${movieId}/videos?api_key=13c164db7b0cbbc91a51acf2fcc65f79`,
      )
        .then(response => response.json())
        .then(data => {
          const trailer = data.results.find(
            video => video.type === 'Trailer' && video.site === 'YouTube',
          )
          if (trailer) {
            this.trailerKey = trailer.key // Guarda el ID del tráiler
          }
        })
        .catch(error => {
          console.error('Error al obtener el tráiler: ' + error)
        })
    },
    watchTrailer() {
      if (this.trailerKey) {
        window.open(
          `https://www.youtube.com/watch?v=${this.trailerKey}`,
          '_blank',
        )
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
      // Redireccionar a la pantalla de la categoría
      this.$emit('changePage', 'DetailCategory', { genreId })
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
  font-family: 'Montserrat', sans-serif;
  background-color: #3f3f3f;
  color: #333;
}

#movieDetails {
  max-width: 1200px;
  margin: 20px auto;
  padding: 20px;
  background-color: #ffffff;
  border-radius: 8px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
}

.movie-content {
  display: flex;
  gap: 20px;
  margin-bottom: 30px;
}

.movie-poster {
  width: 250px;
  border-radius: 8px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

.movie-info {
  flex: 1;
}

.movie-title {
  font-size: 2rem;
  font-weight: bold;
}

.movie-tagline {
  font-style: italic;
  color: #888;
}

.movie-meta p {
  margin: 8px 0;
}

.movie-genres,
.movie-overview {
  margin: 20px 0;
}

.rating {
  margin-top: 20px;
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.rating select {
  width: 150px;
  padding: 5px;
  border-radius: 5px;
}

.save-button,
.delete-button {
  width: 150px;
  padding: 10px;
  font-size: 1rem;
  border-radius: 5px;
  border: none;
  cursor: pointer;
}

.save-button {
  background-color: #28a745;
  color: white;
}

.save-button:hover {
  background-color: #218838;
}

.delete-button {
  background-color: #dc3545;
  color: white;
}

.delete-button:hover {
  background-color: #c82333;
}

.back-button {
  background-color: #007bff;
  color: white;
  padding: 10px 20px;
  border-radius: 5px;
  margin-bottom: 20px;
  font-size: 1rem;
}

.back-button:hover {
  background-color: #0056b3;
}

.movie-cast h2 {
  margin-top: 30px;
}

.carousel-container {
  display: flex;
  overflow-x: auto;
  scroll-snap-type: x mandatory;
  -webkit-overflow-scrolling: touch;
}

.carousel {
  display: flex;
  gap: 10px;
}

.actor-item {
  flex: 0 0 auto;
  scroll-snap-align: start;
  text-align: center;
}

.actor-image {
  width: 100px;
  height: 150px;
  object-fit: cover;
  border-radius: 5px;
}

.trailer-button {
  background-color: #3d3d3d;
  color: white;
  padding: 10px 20px;
  border-radius: 5px;
  cursor: pointer;
  margin-top: 20px;
}

.trailer-button:hover {
  background-color: #1d1d1d;
}
</style>
