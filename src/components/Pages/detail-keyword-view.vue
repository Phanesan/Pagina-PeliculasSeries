<template>
  <div id="main">
    <div
      class="card"
      v-for="movie in movies"
      :key="movie.id"
      @click="viewMovie(movie)"
    >
      <img
        :src="
          'https://www.themoviedb.org/t/p/w600_and_h900_bestv2/' +
          movie.poster_path
        "
        alt="movie poster"
      />
      <h2>{{ movie.title }}</h2>
      <p>
        <strong>Lanzamiento:</strong><br />
        {{ movie.release_date }}
      </p>
      <p><strong>Lenguaje original:</strong> {{ movie.original_language }}</p>
      <p>{{ movie.overview }}</p>
    </div>
  </div>
</template>

<script>
export default {
  name: 'DetailKeywordView',
  data() {
    return {
      movies: [],
    };
  },
  methods: {
    getMovies() {
      const keywordId = 1701;
      fetch(
        `https://api.themoviedb.org/3/discover/movie?api_key=13c164db7b0cbbc91a51acf2fcc65f79&with_keywords=${keywordId}`,
      )
        .then((response) => response.json())
        .then((data) => {
          console.log(data);
          this.movies = data.results;
        })
        .catch((error) => {
          console.log('Error: ' + error);
        });
    },
    viewMovie(movie) {
      console.log(movie);
      const movieId = movie.id;
      window.location.href = `movie.html?id=${movieId}`;
    },
  },
  mounted() {
    console.log('App mounted');
    this.getMovies();
  },
};
</script>

<style>
#main {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 20px;
  padding: 20px;
  justify-content: center;
  justify-items: center;
  max-width: 1200px; 
  margin: 0 auto; 
}

.card {
  background-color: #f0f0f0;
  border-radius: 10px;
  overflow: hidden;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  cursor: pointer;
  transition: transform 0.2s ease;
  display: flex;
  flex-direction: column;
}

.card:hover {
  transform: scale(1.05);
}

.card img {
  width: 100%;
  height: auto;
  object-fit: cover;
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
}
</style>
