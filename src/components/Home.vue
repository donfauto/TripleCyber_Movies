<template>
  <div class="home">
    <h1 class="page-title">Lista top películas</h1>
    <div class="favorites-filter">
      <button @click="toggleSortByFavorites" class="sort-button">
        <font-awesome-icon icon="star"/>
        {{ sortByFavorites ? 'Mostrar todas' : 'Mostrar favoritos' }}
      </button>
    </div>
    <ul class="movie-list">
      <li v-for="movie in sortedMovies" :key="movie.id" class="movie-item">
        <router-link :to="'/movie/' + movie.id" class="movie-router" @mouseover="hoveredMovie = movie"
        @mouseleave="hoveredMovie = null">
          <font-awesome-icon v-if="isFavorite(movie)" icon="star" class="favorite-star" />
          <div style="position: relative;">
            <img :src="getImageUrl(movie.poster_path)"
            alt="Poster"
            class="movie-poster"
           >
            <div class="eye-icon" v-show="hoveredMovie === movie">
              <font-awesome-icon icon="eye" class="eye-icon" />
            </div>
          </div>
      
          <div class="movie-details">
            <h2 class="movie-title">{{ movie.title }}</h2>
            <p class="movie-release-date">{{ formatDate(movie.release_date) }}</p>
            <p class="movie-rating">Rating: {{ movie.vote_average }}</p>
          </div>
        </router-link>
        <div class="movie-details-btn">
            <button v-if="isFavorite(movie)" @click="removeFromFavorites(movie)" class="btn-add-favorite">Quitar de favoritos</button>
            <button v-if="!isFavorite(movie)" @click="addToFavorites(movie)" class="btn-add-favorite">Agregar a favoritos</button>
          </div>
      </li>
    </ul>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      movies: [],
      favorites: [],
      sortByFavorites: false,
      hoveredMovie: null,
    };
  },
  mounted() {
    this.loadFavorites();
    this.fetchMovies();
  },
  computed: {
    sortedMovies() {
      if (this.sortByFavorites) {
        return this.movies.filter(movie => this.isFavorite(movie));
      } else {
        return this.movies;
      }
    },
  },
  methods: {
    toggleSortByFavorites() {
      this.sortByFavorites = !this.sortByFavorites;
    },
    fetchMovies() {
      axios
        .get('https://api.themoviedb.org/3/movie/top_rated', {
          params: {
            api_key: 'bf091621962bdf5c30339e874a2a0c1a',
            language: 'en-US',
            page: 1,
          },
        })
        .then(response => {
          this.movies = response.data.results;
        })
        .catch(error => {
          console.error(error);
        });
    },
    addToFavorites(movie) {
      if (!this.isFavorite(movie)) {
        this.favorites.push(movie);
        this.saveFavorites();
      }
    },
    removeFromFavorites(movie) {
      this.favorites = this.favorites.filter(fav => fav.id !== movie.id);
      this.saveFavorites();
    },
    isFavorite(movie) {
      return this.favorites.some(fav => fav.id === movie.id);
    },
    saveFavorites() {
      localStorage.setItem('favorites', JSON.stringify(this.favorites));
    },
    loadFavorites() {
      const favorites = localStorage.getItem('favorites');
      if (favorites) {
        this.favorites = JSON.parse(favorites);
      }
    },
    getImageUrl(path) {
      return `https://image.tmdb.org/t/p/w200${path}`;
    },
    formatDate(date) {
      const options = { year: 'numeric', month: 'long', day: 'numeric' };
      return new Date(date).toLocaleDateString('en-US', options);
    },
  },
};
</script>

<style>
.movie-details-btn{
  margin-top: 10px;
  margin-bottom: 20px;
}

.eye-icon {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  font-size: 24px;
  color: #ffffff;
}

.sort-button{
  font-size: 18px;
}

.favorite-star {
  position: absolute;
  right: 0px;
  color: #FFD700;
  font-size: 20px;
  z-index: 1;
}
.movie-router{
  justify-content: center;
  align-items: center;
  text-decoration:none;
}
.movie-router:hover{
  opacity: 80%;
  background-color: #0c1537;
}
body{
  background-color: #080f28;
}

.movie-list {
  display: flex;
  flex-wrap: wrap;
  justify-content: flex-start;
}

.movie-item {
  flex: 0 0 150px;
  margin: 10px;
  border-radius: 5px;
  color: #FFF;
  list-style: none;
  position: relative;
  justify-content: flex-start;
}

.movie-item:hover {
  cursor: pointer;
}

.movie-poster {
  width: 100%;
  height: auto;
  justify-content: center;
  margin-bottom: 10px;
  background-color: black;

}

.movie-title {
  font-family: Arial, sans-serif;
  font-size: 10px;
  font-weight: bold;
  margin-bottom: 5px;
  color: white;
}

.movie-release-date,
.movie-rating {
  font-family: Arial, sans-serif;
  font-size: 12px;
  margin: 0;
  color: #BBDEFB;
}

</style>
