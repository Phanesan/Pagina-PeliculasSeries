<template>
  <div id="main" class="categories">
    <div v-if="items.length > 0">
      <div class="genre-filter">
        <label for="media-type">Tipo:</label>
        <select id="media-type" v-model="mediaType" @change="getItemsByCategory">
          <option value="movie">Películas</option>
          <option value="tv">Series</option>
        </select>
        
        <label for="genre">Género:</label>
        <input type="text" id="genre" :value="genreName" readonly />
      </div>

      <div class="item-list">
        <div class="item" v-for="item in items" :key="item.id">
          <div class="poster">
            <img :src="'https://www.themoviedb.org/t/p/w200/' + (mediaType === 'movie' ? item.poster_path : item.poster_path)" alt="item poster">
          </div>
          <div class="item-details">
            <h2>{{ mediaType === 'movie' ? item.title : item.name }}</h2>
            <p>{{ item.overview }}</p>
            <p>Fecha de Publicación: {{ mediaType === 'movie' ? item.release_date : item.first_air_date }}</p>
            <button :href="mediaType === 'movie' ? 'movie.html?id=' + item.id : 'tv.html?id=' + item.id" class="view-more">Ver detalles</button>
          </div>
        </div>
      </div>
    </div>
    <div v-else>
      <p>No se encontraron {{ mediaType === 'movie' ? 'películas' : 'series' }} para este género.</p>
    </div>
  </div>
</template>

<script>
export default {
  name: 'DetailCategoryView',

  data() {
    return {
      items: [], 
      genreName: '',
      mediaType: 'movie',
    };
  },
  methods: {
    getItemsByCategory() {
      const categoryId = 35; 
      const endpoint = this.mediaType === 'movie' ? 'discover/movie' : 'discover/tv';

      fetch(`https://api.themoviedb.org/3/${endpoint}?with_genres=${categoryId}&sort_by=popularity.desc&api_key=b4014e28c8f91a3d85b70da33bb5afb2`)
        .then(response => response.json())
        .then(data => {
          console.log("Datos recibidos:", data); 
          this.items = Array.isArray(data.results) ? data.results : [];
          console.log("Elementos:", this.items); 
        })
        .catch(error => {
          console.error("Error al obtener elementos por categoría:", error);
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
          }
        })
        .catch(error => {
          console.error("Error al obtener el nombre del género:", error);
        });
    },
  },
  mounted() {
    this.getItemsByCategory(); 
  },
}
</script>



<style>
.genre-filter {
  display: flex;
  align-items: center;
  margin-bottom: 20px;
  border: 1px solid black;
  padding: 10px;
  width: 60%; 
  max-width: 1200px; 
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
.item-list {
  display: flex;
  flex-direction: column;
  gap: 20px;
  width: 100%;
  margin: 0 auto;
}

.item {
  display: flex;
  border: 1px solid black;
  background-color: var(--oxford-blue);
  padding: 10px;
  border-radius: 8px;
  align-items: stretch; /* Asegura que todas las tarjetas tengan la misma altura */
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

.poster {
  width: 150px;
  margin-right: 20px;
}

.poster img {
  width: 100px;
  height: 150px;
  object-fit: cover;
}

.item-details {
  display: flex;
  flex-direction: column;
  justify-content: space-between; /* Distribuye el contenido de manera uniforme */
  align-items: flex-start; 
  flex-grow: 1;
}

.item-details h2 {
  font-size: 20px;
  margin: 0 0 10px;
  color: #ffffff;
}

.item-details p {
  font-size: 16px;
  margin: 0 0 10px;
  color: #ffffff;
}


button {
  padding: 8px 12px;
  background-color: #007bff;
  color: #ffffff;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

button:hover {
  background-color: #0056b3;
}

@media (min-width: 1000px) {
  .item-list {
    width: 65%; 
  }
  
}


</style>
