<template>
  <div id="app">
    <div class="container">
      <h1>Tendencias</h1>
      <div class="card-container" ref="carousel">
        <div
          class="card"
          v-for="(movie, index) in movies"
          :key="index"
          @click="goToMovie(movie.id)"
        >
          <img :src="getImageUrl(movie.poster_path)" alt="Movie Poster" />
          <div>
            <h3>{{ movie.title }}</h3>
            <p>Fecha de estreno</p>
            <p>{{ movie.release_date }}</p>
            <p>Popularidad</p>
            <p>{{ movie.vote_average.toFixed(1) }}</p>
          </div>
          <a href="#" class="button">Ver Detalles</a>
        </div>
      </div>

      <h1>Lo más popular</h1>
      <div class="card-container" ref="carousel">
        <div
          class="card"
          v-for="(movie, index) in movies"
          :key="index"
          @click="goToMovie(movie.id)"
        >
          <img :src="getImageUrl(movie.poster_path)" alt="Movie Poster" />
          <div>
            <h3>{{ movie.title }}</h3>
            <p>Fecha de estreno</p>
            <p>{{ movie.release_date }}</p>
            <p>Popularidad</p>
            <p>{{ movie.vote_average.toFixed(1) }}</p>
          </div>
          <a href="#" class="button">Ver Detalles</a>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Home',
  data() {
    return {
      movies: [],
    }
  },
  methods: {
    getImageUrl(path) {
      return path
        ? `https://image.tmdb.org/t/p/w500${path}`
        : 'https://via.placeholder.com/150'
    },

    goToMovie(id) {
      window.location.href = `movie.html?id=${id}`
    },

    fetchMovies() {
      const apiKey =
        'eyJhbGciOiJIUzI1NiJ9.eyJhdWQiOiI3NTQxODk4NTc3NWNjZTRkMjZjNjc0NTc1ZjRmNDliMiIsIm5iZiI6MTcyOTMxNDc2Ny4zMzYzODQsInN1YiI6IjY3MDc0YjA2ZTQ2YTEyYTE5NDE5ZTg4OCIsInNjb3BlcyI6WyJhcGlfcmVhZCJdLCJ2ZXJzaW9uIjoxfQ.u8RuyookdzmOJ_p30WXq1LP9gnp4b55ups-Ne1Fj-Q4'
      const apiUrl = 'https://api.themoviedb.org/3/discover/movie'

      const myHeaders = new Headers()
      myHeaders.append('Authorization', `Bearer ${apiKey}`)

      const requestOptions = {
        method: 'GET',
        headers: myHeaders,
        redirect: 'follow',
      }

      fetch(apiUrl, requestOptions)
        .then(response => response.json())
        .then(data => {
          console.log(data.results)
          this.movies = data.results
        })
        .catch(error => console.error('Error:', error))
    },
  },
  mounted() {
    this.fetchMovies()
  },
}
</script>

<style>
body {
  font-family: Arial, sans-serif;
  margin: 0;
  padding: 0;
}

.container {
  width: 55%;
  margin: auto;
  padding: 20px;
}

h1 {
  text-align: left;
  margin-top: 10px;
  margin-bottom: 10px;
  color: white;
}

.card-container {
  display: flex;
  overflow-x: auto;
  gap: 1rem;
  scroll-behavior: smooth;
  padding: 1rem 0;
}

.card {
  width: 150px;
  background-color: #f4f4f4;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 8px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  flex-shrink: 0;
  color: #202020;
}

.card img {
  max-width: 100%;
  border-radius: 8px;
}

.card h3 {
  font-weight: bold;
}

.button {
  text-align: center;
  display: inline-block;
  margin-top: 10px;
  padding: 10px 15px;
  background-color: #5c5c5c;
  color: white;
  text-decoration: none;
  border-radius: 5px;
}

.button:hover {
  background-color: #1f1f1f;
}
</style>
