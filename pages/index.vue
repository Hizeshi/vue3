<script setup lang="ts">
import {useFilmStore} from "~/stores/film";
import {useCategoryStore} from "~/stores/category";
import {useCountryStore} from "~/stores/country";

const categoryStore = useCategoryStore();
const filmStore = useFilmStore()
const countryStore = useCountryStore();
const category = ref(null);
const country = ref(null);
const sort = ref('name');
const filter = () => {
  filmStore.addCategoryToParams(category.value);
  filmStore.addCountryToParams(country.value);
  filmStore.addSortToParams(sort.value);
  filmStore.fetchFilms();
};

const reset = () => {
  category.value = null;
  country.value = null;
  sort.value = 'name';
  filter();
};

const goto = (page: number) => {
  page = (page < 0) ? 1 : page;
  page = (page > filmStore.countPages) ? filmStore.countPages : page
  filmsStore.currentPage = page;
  filmStore.fetchFilms();

}
</script>

<template>
  <div class="row mt-2">
    <div class="col-md-4">
      <select v-model="category" @change="filter" class="form-select" aria-label="Default select example">
        <option :value = "null" selected>Select genre</option>
        <option v-for="category in categoryStore.categories"
                :key="category.id"
                :value="category.id">
          {{ category.name }} {{ category.filmCount }}
        </option>
      </select>
    </div>
    <div class="col-md-4">
      <select v-model="country" @change="filter" class="form-select" aria-label="Default select example">
        <option :value="null" selected>All Countries</option>
        <option
            v-for="country in countryStore.countries"
            :key="country.id"
            :value="country.id">
          {{ country.name }}
        </option>
      </select>
    </div>
    <div class="col-md-2">
      <select v-model="sort" @change="filter" class="form-select" aria-label="Default select example">
        <option value="1">Name</option>
        <option value="2">Year</option>
        <option value="3">Rating</option>
      </select>
    </div>
    <div class="col-md-2">   <button type="button" class="btn btn-outline-info" @click="reset">Reset</button></div>
  </div>
  <div v-if="!filmStore.isLoading" class="row row-cols-1 row-cols-md-3 g-4 mt-2">
    <div class="col" v-for="film in filmStore.films" :key="film.id">
      <div class="card h-100">
        <img v-if="film.link_img" :src="film.link_img" class="card-img-top" alt="...">
        <img v-else="film.link_img" src="https://m.media-amazon.com/images/I/71KPnecVSNL._SL1108_.jpg" class="card-img-top" alt="...">
        <div class="card-body">
          <h5 class="card-title"> {{ film.name}}</h5>
          <p class="card-text"> {{ film.ratingAvg}}</p>
          <p class="card-text"> {{ film.duration }} min.</p>
          <p class="card-text"> {{ film.year_of_issue }}</p>
          <p class="card-text">
            <template v-for="(category, index) in film.categories" :key="category.id">
              {{ category.name + (index + 1 < film.categories.length ? ", " : "") }}
            </template>
          </p>
        </div>
        <button type="button" class="btn btn-success">Watch</button>
      </div>
    </div>
    <!--    <div class="col">-->
    <!--      <div class="card h-100">-->
    <!--        <img src="https://avatars.mds.yandex.net/i?id=5d7ddddd5551d5dd33bf4c76b9820c6e_l-9245043-images-thumbs&n=13" class="card-img-top" alt="...">-->
    <!--        <div class="card-body">-->
    <!--          <h5 class="card-title">Интерстеллар</h5>-->
    <!--          <p class="card-text">5.0</p>-->
    <!--          <p class="card-text">169 min</p>-->
    <!--          <p class="card-text">Action, Fantastic</p>-->
    <!--        </div>-->
    <!--        <button type="button" class="btn btn-success">Success</button>-->
    <!--      </div>-->
    <!--    </div>-->
    <!--    <div class="col">-->
    <!--      <div class="card h-100">-->
    <!--        <img src="https://avatars.mds.yandex.net/i?id=58a50c9776a77301a0b00a2390d6ce90_l-10752752-images-thumbs&n=13" class="card-img-top" alt="...">-->
    <!--        <div class="card-body">-->
    <!--          <h5 class="card-title">Мост в Терабитию</h5>-->
    <!--          <p class="card-text">4.4</p>-->
    <!--          <p class="card-text">93 min</p>-->
    <!--          <p class="card-text">Drama, Fantasy</p>-->
    <!--        </div>-->
    <!--        <button type="button" class="btn btn-success">Success</button>-->
    <!--      </div>-->
    <!--    </div>-->
  </div>
  <div v-else class="d-flex justify-content-center mt-4">
    <div class="spinner-border" role="status">
      <span class="visually-hidden">Loading...</span>
    </div>
  </div>
  <nav class="mt-4 d-flex justify-content-center" aria-label="Page navigation example">
    <ul class="pagination">
      <li class="page-item" :class="{ disabled: filmStore.currentPage === 1 }">
        <a class="page-link" href="#" aria-label="Previous" @click.prevent="filmStore.goToPage(filmStore.currentPage - 1)">
          <span aria-hidden="true">&laquo;</span>
        </a>
      </li>
      <li class="page-item" v-for="num in filmStore.countPage" :key="num">
        <a class="page-link" :class="{ active: filmStore.currentPage === num }" @click.prevent="filmStore.goToPage(num)">
          {{ num }}
        </a>
      </li>
      <li class="page-item" :class="{ disabled: filmStore.currentPage === filmStore.countPage }">
        <a class="page-link" href="#" aria-label="Next" @click.prevent="filmStore.goToPage(filmStore.currentPage + 1)">
          <span aria-hidden="true">&raquo;</span>
        </a>
      </li>
    </ul>
  </nav>
</template>

<style scoped>
.card-img-top {
  height: 500px;
  object-fit: cover;
}
</style>