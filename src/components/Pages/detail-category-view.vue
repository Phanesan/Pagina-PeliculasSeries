<template>
  <div id="main" class="categories">
    <div v-if="movies.length > 0">
      <!-- Filtro de Género -->
      <div class="genre-filter">
        <label for="genre">Género:</label>
        <input type="text" id="genre" :value="genreName" readonly />
      </div>

      <div class="movie-list">
        <div class="movie-item2" v-for="movie in movies" :key="movie.id">
          <div class="poster">
            <img :src="'https://www.themoviedb.org/t/p/w200/' + movie.poster_path" alt="movie poster">
          </div>
          <div class="movie-details">
            <h2>{{ movie.title }}</h2>
            <p>{{ movie.overview }}</p>
            <p>Fecha de Publicación: {{ movie.release_date }}</p>
            <a :href="'movie.html?id=' + movie.id" class="view-more">Ver detalles</a>
          </div>
        </div>
      </div>
    </div>
    <div v-else>
      <p>No se encontraron películas para este género.</p>
    </div>
  </div>
</template>


<script>
export default {
  name: 'DetailCategoryView',

  data() {
    return {
      movies: [],
      genreName: '',
    };
  },
  methods: {
                getMoviesByCategory() {
                    const categoryId = 12;

                    //const categoryId = new URLSearchParams(window.location.search).get('id');
                    console.log('Category ID:', categoryId);


                    fetch(`https://api.themoviedb.org/3/discover/movie?with_genres=${categoryId}&api_key=b4014e28c8f91a3d85b70da33bb5afb2`)
                        .then(response => response.json())
                        .then(data => {
                            this.movies = data.results;
                            
                        })
                        .catch(error => {
                            console.error("Error al obtener películas por categoría:", error);
                        });
                    
                    this.getGenreName(categoryId);
                },
                getGenreName(categoryId) {
                    fetch(`https://api.themoviedb.org/3/genre/movie/list?api_key=b4014e28c8f91a3d85b70da33bb5afb2`)
                        .then(response => response.json())
                        .then(data => {
                            const genre = data.genres.find(g => g.id == categoryId);
                            if (genre) {
                                this.genreName = genre.name;
                                console.log(this.genreName)
                            }
                        })
                        .catch(error => {
                            console.error("Error al obtener el nombre del género:", error);
                        });
                },
            },
            mounted() {
                this.getMoviesByCategory();
            }
,

}
</script>


<style>
.genre-filter {
  display: flex;
  align-items: center;
  margin-bottom: 20px;
  border: 1px solid black;
  padding: 10px;
  width: 1200px;
  margin: 20px auto;
  border-radius: 8px;
  background-color: var(--slate-blue);
}

.genre-filter label {
  margin-right: 10px;
}

#genre {
  padding: 5px;
  font-size: 16px;
  border: none;
  outline: none;
}

.clear-filter {
  background-color: transparent;
  border: none;
  font-size: 18px;
  cursor: pointer;
  margin-left: 10px;
}

.movie-list {
  display: flex;
  flex-direction: column;
  gap: 20px;
  width: 80%;
  margin: 0 auto;
}

.movie-item2 {
  display: flex;
  border: 1px solid black;
  background-color: var(--oxford-blue);
  padding: 10px;
}

.poster {
  width: 150px;
  margin-right: 20px;
}

.poster img {
  width: 100%;
  height: auto;
}

.movie-details {
  flex-grow: 1;
}

.movie-details h2 {
  font-size: 20px;
  margin: 0 0 10px;
  font-size: 1.5em;
}

.movie-details p {
  font-size: 16px;
  margin: 0 0 10px;
  color: #555;
}

.view-more {
  padding: 5px 10px;
  background-color: #007BFF;
  color: white;
  border: none;
  cursor: pointer;
  font-size: 16px;
}

.view-more:hover {
  background-color: #0056b3;
}

</style>
