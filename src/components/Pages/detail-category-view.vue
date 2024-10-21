<template>
  <div id="main" class="categories">
    <div class="genre-filter">
      <label for="media-type">Tipo:</label>
      <select id="media-type" v-model="mediaType" @change="getItemsByCategory">
        <option value="movie">Películas</option>
        <option value="tv">Series</option>
      </select>

      <label for="popularity-filter">Popularidad:</label>
      <select id="popularity-filter" v-model="popularityFilter" @change="getItemsByCategory">
        <option value="popularity.desc">Más populares</option>
        <option value="popularity.asc">Menos populares</option>
      </select>

      <label class="genre" for="genre">Género:</label>
      <input type="text" id="genre" :value="genreName" readonly />
    </div>

    <div class="item-list">
      <div v-if="noResults" class="no-results">
        <p>No se encontraron {{ mediaType === 'movie' ? 'películas' : 'series' }} para este género.</p>
      </div>

      <div v-else>
        <div class="item" v-for="(item, index) in items.slice(0, visibleItemsCount)" :key="item.id">
          <div class="poster">
            <img v-if="item.poster_path" :src="'https://www.themoviedb.org/t/p/w200/' + item.poster_path"
              :alt="mediaType === 'movie' ? item.title : item.name" class="movie-poster">
            <img v-else src="../../assets/img/movie.png" alt="Imagen de reemplazo" class="poster-image" />
          </div>
          <div class="item-details">
            <h2>{{ mediaType === 'movie' ? item.title : item.name }}</h2>
            <p>Fecha de Publicación: {{ mediaType === 'movie' ? item.release_date : item.first_air_date }}</p>
            <p>{{ item.overview }}</p>
            <button @click="$emit('changePage', 'DetailMovie', { id: item.id })" class="view-more">Ver detalles</button>
          </div>
        </div>
      </div>
    </div>

    <div class="pagination-controls">
      <button v-if="currentPage > 1" @click="previousPage" class="load-more">Página anterior</button>
      <span class="current-page">Página {{ currentPage }} de {{ totalPages }}</span>
      <button v-if="currentPage < totalPages" @click="nextPage" class="load-more">Siguiente página</button>
    </div>
  </div>

</template>

<script>
export default {
  name: 'DetailCategoryView',
  props: {
    payload: Object
  },

  data() {
    return {
      items: [],
      genreName: '',
      mediaType: 'movie',
      popularityFilter: 'popularity.desc',
      visibleItemsCount: 10,
      noResults: false,
      currentPage: 1,
      totalPages: 1,

    };
  },
  methods: {
    getItemsByCategory() {
      const categoryId = this.payload.id;
      const endpoint = this.mediaType === 'movie' ? 'discover/movie' : 'discover/tv';

      fetch(`https://api.themoviedb.org/3/${endpoint}?with_genres=${categoryId}&sort_by=${this.popularityFilter}&api_key=b4014e28c8f91a3d85b70da33bb5afb2&page=${this.currentPage}`)
        .then(response => response.json())
        .then(data => {
          console.log("Datos recibidos:", data);
          this.items = Array.isArray(data.results) ? data.results : [];
          this.noResults = this.items.length === 0;
          this.totalPages = data.total_pages;
          console.log("Elementos:", this.items);
          this.visibleItemsCount = this.items.length;
        })
        .catch(error => {
          console.error("Error al obtener elementos por categoría:", error);
        });

      this.getGenreName(categoryId);
    },
    loadMoreItems() {
      this.visibleItemsCount += 10;
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
    nextPage() {
      if (this.currentPage < this.totalPages) {
        window.scrollTo({
          top: 0,
          behavior: 'smooth'
        });
        this.currentPage++;
        this.getItemsByCategory();
      }
    },
    previousPage() {
      if (this.currentPage > 1) {
        window.scrollTo({
          top: 0,
          behavior: 'smooth'
        });
        this.currentPage--;
        this.getItemsByCategory();
      }
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

.genre {
  margin-left: 10px;
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
  min-height: 300px;
}

.item {
  display: flex;
  border: 1px solid black;
  background-color: var(--oxford-blue);
  padding: 10px;
  border-radius: 8px;
  align-items: stretch;
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
  justify-content: space-between;
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


.view-more {
  padding: 8px 12px;
  background-color: var(--palatinate-blue);
  color: #ffffff;
  border: none;
  margin-left: 90%;
  border-radius: 4px;
  cursor: pointer;
}

.view-more:hover {
  background-color: var(--slate-blue);
}

.no-results {
  text-align: center;
  margin: 20px 0;
  color: #ff0000;
  font-size: 18px;
}

.pagination-controls {
  display: flex;
  justify-content: center;
  margin-top: 20px;
}

.load-more {
  padding: 10px 20px;
  background-color: var(--palatinate-blue);
  color: #ffffff;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
  margin: 0 10px;
  transition: background-color 0.3s, transform 0.3s;
}

.load-more:hover {
  background-color: var(--slate-blue);
  transform: translateY(-2px);
}

.current-page {
  align-self: center;
  margin: 0 15px;
  color: #ffffff;
  font-size: 16px;
}


@media (min-width: 1000px) {

  .item-list {
    width: 65%;
  }

}
</style>
