<template>
    <div class="movie-detail">
      <h1 class="btn-back-title"><router-link :to="'/'"  class="btn-back">  ← Atrás...</router-link></h1>
      <h1 class="page-title">Detalle de la película</h1>
      <div class="movie-container">
        <img :src="getImageUrl(movie.poster_path)" alt="Poster" class="movie-poster">
        <div class="movie-details">
          <h2 class="movie-title">{{ movie.title }}</h2>
          <p class="movie-release-date"><span>Release:</span> {{ formatDate(movie.release_date) }}</p>
          <p class="movie-rating"><span>Rating:</span> {{ movie.vote_average }}/10</p>
          <p class="movie-overview">{{ movie.overview }}</p>
        </div>
      </div>
    </div>
  </template>
  
  <script>
  import axios from 'axios';
  
  export default {
    data() {
      return {
        movie: {},
        favorites: []
      };
    },
    mounted() {
      const movieId = this.$route.params.id;
      axios.get(`https://api.themoviedb.org/3/movie/${movieId}`, {
        params: {
          api_key: 'bf091621962bdf5c30339e874a2a0c1a',
          language: 'en-US',
        },
      })
        .then(response => {
          this.movie = response.data;
        })
        .catch(error => {
          console.error(error);
        });
    },
    methods: {
      addToFavorites(movie) {
        if (!this.isFavorite(movie)) {
            this.favorites.push(movie);
            this.saveFavorites();
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
  
  <!-- <style scoped> -->
  <style>
.btn-back{
  font-size: 24px;
  margin-bottom: 20px;
  color: white;
  text-decoration: none;
}
.btn-back:hover{
  text-decoration:underline;
}

.btn-back-title{
  font-size: 24px;
  margin-bottom: 20px;
  color: white;
}

  .movie-detail {
    margin-top: 40px;
  }
  
  .page-title {
    font-size: 24px;
    margin-bottom: 20px;
    color: white;
  }
  
  .movie-container {
    /* display: flex; */
    position: relative;
    align-items: center;
  }
  
  .movie-poster {
    width: 200px;
    height: 300px;
    object-fit: cover;
  }
  
  .movie-details {
    flex-grow: 1;
    height: 80px;
    padding: 0 !important;
    /* margin: 20px; */
  }

  .movie-container .movie-details{
    height: auto;
  }

  .movie-title {
    font-size: 18px;
    margin-bottom: 5px;
  }
  
  .movie-release-date,
  .movie-rating {
    margin: 0;
    font-size: 14px;
    color: rgb(197, 197, 197);
  }
 
  .movie-release-date span,
  .movie-rating span{
    font-weight: bold;
  }

  .movie-overview{
    margin-top: 10px;
    font-size: 14px;
    color: rgb(197, 197, 197);
    text-align: justify;
  }
  
  .btn-add-favorite {
    padding: 8px 16px;
    background-color: #007bff;
    color: #fff;
    border: none;
    border-radius: 4px;
    cursor: pointer;
    width: 100%;
  }
  .remove{
    background-color: #0056b3  !important;
  }
  
  .btn-add-favorite:hover {
    background-color: #0056b3;
  }
  </style>
  