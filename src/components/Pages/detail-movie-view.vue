<template>
    <div id="movieDetails">
      <button class="back-button" @click="goBack">Regresar</button>
  
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
          <h1>{{ movie.title }}</h1>
          <p>{{ movie.tagline }}</p>
          <p><strong>Lanzamiento:</strong> {{ movie.release_date }}</p>
          <p class="genres">
            <strong>Géneros:</strong>
            <span v-for="(genre, index) in movie.genres" :key="genre.id">
              {{ genre.name }}<span v-if="index < movie.genres.length - 1">, </span>
            </span>
          </p>
          <p><strong>Sinopsis:</strong> {{ movie.overview }}</p>
          <p><strong>Lenguaje original:</strong> {{ movie.original_language }}</p>
          <p><strong>Popularidad:</strong> {{ movie.popularity }}</p>
          <p><strong>Votos:</strong> {{ movie.vote_count }}</p>
          <p><strong>Calificación:</strong> {{ movie.vote_average }} / 10</p>
  
          <div class="rating">
            <label for="rating">Tu calificación:</label>
            <select v-model="userRating" id="rating">
              <option disabled value="">Selecciona un rating</option>
              <option v-for="n in 5" :key="n" :value="n">{{ n }}</option>
            </select>
            <br />
            <button class="save-button" @click="saveRating">Guardar calificación</button>
            <button class="delete-button" @click="deleteRating">Eliminar calificación</button>
          </div>
        </div>
      </div>
  
      <!-- Sección para mostrar actores con imágenes y carrusel -->
      <div class="movie-cast">
        <h2>Reparto</h2>
        <div class="carousel-container">
          <button class="carousel-button" @click="prevActors">⬅️</button>
          <div class="carousel">
            <div
              class="actor-item"
              v-for="(actor, index) in visibleCast"
              :key="actor.cast_id"
            >
              <img
                v-if="actor.profile_path"
                :src="`https://www.themoviedb.org/t/p/w185${actor.profile_path}`"
                alt="Foto de {{ actor.name }}"
                class="actor-image"
              />
              <p><strong>{{ actor.name }}</strong></p>
              <p>como {{ actor.character }}</p>
            </div>
          </div>
          <button class="carousel-button" @click="nextActors">➡️</button>
        </div>
      </div>
    </div>
  
    <button @click="$emit('changePage', 'DetailArtist')">Detail artist</button>
  </template>
  
  <script>
  export default {
    name: 'DetailMovieView',
    data() {
      return {
        movie: {},
        cast: [], // Array para almacenar el reparto completo
        visibleCast: [], // Array para almacenar los actores visibles en el carrusel
        currentIndex: 0, // Índice actual del carrusel
        itemsPerPage: 6, // Cantidad de actores por página
        userRating: '',
      }
    },
    methods: {
      fetchMovieDetails() {
        const urlParams = new URLSearchParams(window.location.search)
        const movieId = urlParams.get('id')
  
        fetch(
          `https://api.themoviedb.org/3/movie/117?api_key=13c164db7b0cbbc91a51acf2fcc65f79`,
        )
          .then(response => response.json())
          .then(data => {
            this.movie = data
            document.title = this.movie.title
            this.loadRating(117)
            this.fetchMovieCast(117)
          })
          .catch(error => {
            console.error('Error al obtener los detalles de la película: ' + error)
          })
      },
      fetchMovieCast(movieId) {
        fetch(`https://api.themoviedb.org/3/movie/117/credits?api_key=13c164db7b0cbbc91a51acf2fcc65f79`)
          .then(response => response.json())
          .then(data => {
            this.cast = data.cast
            this.updateVisibleCast()
          })
          .catch(error => {
            console.error('Error al obtener el reparto de la película: ' + error)
          })
      },
      updateVisibleCast() {
        this.visibleCast = this.cast.slice(this.currentIndex, this.currentIndex + this.itemsPerPage)
      },
      prevActors() {
        if (this.currentIndex > 0) {
          this.currentIndex -= this.itemsPerPage
          this.updateVisibleCast()
        }
      },
      nextActors() {
        if (this.currentIndex + this.itemsPerPage < this.cast.length) {
          this.currentIndex += this.itemsPerPage
          this.updateVisibleCast()
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
    background-color: rgb(31, 84, 131);
    color: #fff;
  }
  
  #movieDetails {
    max-width: 1300px;
    margin: 20px auto;
    padding: 20px;
    background-color: rgb(68, 148, 219);
    border-radius: 8px;
    box-shadow: 0 4px 8px #444b6e8a;
  }
  
  .movie-content {
    display: flex;
    align-items: flex-start;
    gap: 20px;
  }
  
  .movie-poster {
    width: 250px;
    height: auto;
    object-fit: cover;
    border-radius: 5px;
  }
  
  .movie-info {
    flex: 1;
  }
  
  .movie-cast {
    margin-top: 30px;
  }
  
  .carousel-container {
    display: flex;
    align-items: center;
  }
  
  .carousel {
    display: flex;
    overflow: hidden;
    width: 100%;
  }
  
  .actor-item {
    flex: 1 0 16.6%;
    display: flex;
    flex-direction: column;
    align-items: center;
    text-align: center;
    margin: 0 10px;
  }
  
  .actor-image {
    width: 100px;
    height: 150px;
    object-fit: cover;
    border-radius: 5px;
  }
  
  .carousel-button {
    background-color: #444b6e;
    color: #fff;
    border: none;
    padding: 10px 20px;
    cursor: pointer;
    border-radius: 5px;
    font-size: 18px;
  }
  
  .carousel-button:hover {
    background-color: #3a425d;
  }
  </style>
  