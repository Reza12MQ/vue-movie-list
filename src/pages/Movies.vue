<template>
    <div class="card my-2">
        <div class="card-body">
            <div class="d-flex">
                <input
                v-model="title"
                class="form-control me-2"
                type="search"
                placeholder="Search Movie"
                aria-label="Search"
                v-on:keyup.enter="searchMovie()"
                />
                <button class="btn btn-primary" @click="searchMovie()">
                Search
                </button>
            </div>
        </div>
    </div>
    <div class="my-3">
        <h4>Search Result</h4>
    </div>

    <div class="row row-cols-4 g-4">
        <div v-for="item in items" :key="item.imdbID" class="col">
          <div class="card">
            <div @click="popupImage(item.Poster)">
              <img
              v-bind:src="item.Poster"
              class="card-img-top"
              v-bind:alt="item.Title"
              />
            </div>
              <div class="card-body">
                <h5 class="card-title">{{ item.Title }}</h5>
                <p class="card-text">Year: {{ item.Year }}</p>
                <router-link :to="'/movie/'+item.imdbID" class="btn btn-outline-primary">Movie Detail</router-link>
              </div>
          </div>
        </div>
    </div>

    <div class="modal fade" ref="exampleModal" tabindex="-1" aria-hidden="true">
      <div class="modal-dialog modal-dialog-centered">
        <div class="modal-content">
          <div class="modal-header">
            <button type="button" class="btn-close" @click="modal.hide()" aria-label="Close"></button>
          </div>
          <div class="modal-body">
            <div class="d-flex justify-content-center">
              <img
              v-bind:src="imgdata"
              />
            </div>
          </div>
        </div>
      </div>
    </div>

</template>

<script>
import axios from "axios";
import { Modal } from 'bootstrap';

export default {
  name: "App",

  data() {
    return {
      items: [],
      search: 1,
      page: 1,
      title: "Batman",
      totalResults: null,
      modal: null,
      imgdata: null
    };
  },

  watch: {
    search(val, oldval) {
      console.log(`new: ${val}, old: ${oldval}`);
    },
  },

  methods: {
    async initialData() {
      let result = await axios.get("http://www.omdbapi.com/", {
        params: this.params,
      });
      this.items = result.data.Search;
      this.totalResults = result.data.totalResults;
    },

    searchMovie() {
      this.page = 1;
      this.initialData();
    },

    getNextUser() {
      window.onscroll = () => {
        if (this.totalResults && this.items.length < this.totalResults) {
          let bottomOfWindow =
            document.documentElement.scrollTop + window.innerHeight >
            document.documentElement.offsetHeight - 100;

          if (bottomOfWindow) {
            this.page++;
            axios
              .get("http://www.omdbapi.com/", {
                params: this.params,
              })
              .then((response) => {
                this.items = [...this.items, ...response.data.Search];
              })
              .catch((error) => {
                console.log(error);
              });
          }
        }
      };
    },

    popupImage(imgUrl) {
      this.imgdata = imgUrl;
      this.modal.show();
    }
  },

  computed: {
    params() {
      const params = new URLSearchParams();
      params.append("apikey", "715289b");
      params.append("s", this.title);
      params.append("page", this.page);
      return params;
    },
  },

  beforeMount() {
    this.initialData();
  },

  mounted() {
    this.getNextUser();
    this.modal = new Modal(this.$refs.exampleModal);
  },
};
</script>