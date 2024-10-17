<template>
    <button @click="$emit('changePage', ' Home')">Home</button>

    <div id="artist">
        <h1>{{ actor.name }}</h1>
        <img v-if="actor.profile_path" :src="'https://www.themoviedb.org/t/p/w600_and_h900_bestv2/' + actor.profile_path" alt="Actor">
        <p>{{ actor.biography }}</p>

        <h3>Películas en las que ha participado:</h3>
        <ul>
            <li v-for="movie in movies" :key="movie.id">
                <a :href="'movie.html?id=' + movie.id">{{ movie.title }}</a> ({{ movie.release_date }})
            </li>
        </ul>

        <button @click="volver">Volver</button>
    </div>




</template>

<script>
export default {
  name: 'DetailArtistView',

  data() {
                return {
                    actor: {},
                    movies: []
                };
            },
            methods: {
                getActorDetails() {
                    const actorId = new URLSearchParams(window.location.search).get('id');
                    fetch(`https://api.themoviedb.org/3/person/10859?api_key=b4014e28c8f91a3d85b70da33bb5afb2`)
                        .then(response => response.json())
                        .then(data => {
                            this.actor = data;
                            this.getActorMovies(actorId); 
                        })
                        .catch(error => {
                            console.error("Error al obtener los detalles del artista:", error);
                        });
                },

                getActorMovies(actorId) {
                    fetch(`https://api.themoviedb.org/3/person/10859/movie_credits?api_key=b4014e28c8f91a3d85b70da33bb5afb2`)
                        .then(response => response.json())
                        .then(data => {
                            this.movies = data.cast; 
                        })
                        .catch(error => {
                            console.error("Error al obtener las películas del artista:", error);
                        });
                },

                volver() {
                    window.history.back();
                }
            },
            mounted() {
                this.getActorDetails(); 
            }
}
</script>