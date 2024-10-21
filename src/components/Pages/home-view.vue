<template>
  <div id="app">
    <div class="container">
      <img src="../../assets/images/banner.jpg" class="banner" />
      <h1>Tendencias</h1>
      <div class="card-container" ref="carousel">
        <div
          class="card"
          v-for="(popularMovie, index) in popularMovies"
          :key="index"
        >
          <img
            :src="getImageUrl(popularMovie.poster_path)"
            alt="Popular Movie Poster"
            @click="$emit('changePage', 'DetailMovie', { id })"
          />
          <div>
            <h3 @click="$emit('changePage', 'DetailMovie', { id })">
              {{ popularMovie.title }}
            </h3>
            <p>Fecha de estreno</p>
            <p>{{ popularMovie.release_date }}</p>
            <p>Popularidad</p>
            <p>{{ popularMovie.vote_average.toFixed(1) }}</p>
          </div>
          <a class="button" @click="$emit('changePage', 'DetailMovie', { id })"
            >Ver Detalles</a
          >
        </div>
      </div>

      <h1>Lo más popular</h1>
      <div class="card-container" ref="carousel">
        <div class="card" v-for="(movie, index) in topRatedMovies" :key="index">
          <img
            :src="getImageUrl(movie.poster_path)"
            alt="Movie Poster"
            @click="$emit('changePage', 'DetailMovie', { id })"
          />
          <div>
            <h3 @click="$emit('changePage', 'DetailMovie', { id })">
              {{ movie.title }}
            </h3>
            <p>Fecha de estreno</p>
            <p>{{ movie.release_date }}</p>
            <p>Popularidad</p>
            <p>{{ movie.vote_average.toFixed(1) }}</p>
          </div>
          <a class="button" @click="$emit('changePage', 'DetailMovie', { id })"
            >Ver Detalles</a
          >
        </div>
      </div>

      <h1>Ver Gratis</h1>
      <div class="card-container" ref="carousel">
        <div class="card" v-for="(movie, index) in freeWatch" :key="index">
          <img
            :src="getImageUrl(movie.poster_path)"
            alt="Movie Poster"
            @click="$emit('changePage', 'DetailMovie', { id })"
          />
          <div>
            <h3 @click="$emit('changePage', 'DetailMovie', { id })">
              {{ movie.title }}
            </h3>
            <p>Fecha de estreno</p>
            <p>{{ movie.release_date }}</p>
            <p>Popularidad</p>
            <p>{{ movie.vote_average.toFixed(1) }}</p>
          </div>
          <a class="button" @click="$emit('changePage', 'DetailMovie', { id })"
            >Ver Detalles</a
          >
        </div>
      </div>

      <h1>Series o TV</h1>
      <div class="card-container" ref="carousel">
        <div
          class="card"
          v-for="(tvShow, index) in topRatedTVShows"
          :key="index"
        >
          <img
            :src="getImageUrl(tvShow.poster_path)"
            alt="TV Show Poster"
            @click="$emit('changePage', 'DetailSeries', { id })"
          />
          <div>
            <h3 @click="$emit('changePage', 'DetailSeries', { id })">
              {{ tvShow.name }}
            </h3>
            <p>Fecha de estreno</p>
            <p>{{ tvShow.first_air_date }}</p>
            <p>Popularidad</p>
            <p>{{ tvShow.vote_average.toFixed(1) }}</p>
          </div>
          <a class="button" @click="$emit('changePage', 'DetailSeries', { id })"
            >Ver Detalles</a
          >
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
      topRatedMovies: [],
      popularMovies: [],
      freeWatch: [],
      topRatedTVShows: [],
    }
  },
  methods: {
    getImageUrl(path) {
      return path
        ? `https://image.tmdb.org/t/p/w500${path}`
        : 'https://via.placeholder.com/150'
    },

    fetchMovies() {
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
        'https://api.themoviedb.org/3/tv/top_rated?language=en-US&page=1',
        requestOptions,
      )
        .then(response => response.json())
        .then(data => {
          console.log(data.results)
          this.topRatedTVShows = data.results
        })
        .catch(error => console.error('Error:', error))
    },

    fetchTopRatedMovies() {
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
        'https://api.themoviedb.org/3/movie/top_rated?language=en-US&page=1',
        requestOptions,
      )
        .then(response => response.json())
        .then(data => {
          console.log(data.results)
          this.topRatedMovies = data.results
        })
        .catch(error => console.error('Error:', error))
    },

    fetchPopularMovies() {
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
        'https://api.themoviedb.org/3/movie/popular?language=en-US&page=1&region=AE',
        requestOptions,
      )
        .then(response => response.json())
        .then(data => {
          console.log(data.results)
          this.popularMovies = data.results
        })
        .catch(error => console.error('Error:', error))
    },
    fetchFreeWatch() {
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
        'https://api.themoviedb.org/3/discover/movie?with_watch_monetization_types=free&watch_region=US',
        requestOptions,
      )
        .then(response => response.json())
        .then(data => {
          console.log(data.results)
          this.freeWatch = data.results
        })
        .catch(error => console.error('Error:', error))
    },
  },
  mounted() {
    this.fetchMovies()
    this.fetchTopRatedMovies()
    this.fetchPopularMovies()
    this.fetchFreeWatch()
  },
}
</script>

<style>
* {
  box-sizing: border-box;
}

body {
  font-family: Arial, sans-serif;
  margin: 0;
  padding: 0;
  overflow-x: hidden;
}

.container {
  width: 43%;
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
  max-width: 100%;
}

.card {
  width: 200px;
  background-color: #d0dcff;
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
  cursor: pointer;
}

.card h3 {
  font-weight: bold;
  margin-top: 10px;
  margin-bottom: 5px;
  cursor: default;
}

.card h3:hover {
  cursor: pointer;
  color: rgb(3, 22, 97);
  text-decoration: underline;
}

.card p {
  margin: 3px 0;
}
.button {
  text-align: center;
  display: inline-block;
  margin-top: 10px;
  padding: 10px 15px;
  background-color: var(--oxford-blue);
  color: white;
  text-decoration: none;
  border-radius: 5px;
  cursor: pointer;
}

.button:hover {
  background-color: rgb(8, 16, 34);
}

.banner {
  width: 100%;
  height: auto;
  max-height: 500px;
  object-fit: cover;
  display: block;
  margin: 0 auto;
}
</style>
