
<template>
    <div>
        <h5>Movie Detail</h5>
    </div>

    <div class="card mb-3">
        <div class="row g-0">
            <div class="col-md-4">
            <img
            v-bind:src="item.Poster"
            class="card-img-top"
            v-bind:alt="item.Title"
            />
            </div>
            <div class="col-md-8">
            <div class="card-body">
                <h5 class="card-title">{{ item.Title }}</h5>
                <p class="card-text">Release Date: {{ item.Released }}</p>
                <p class="card-text">{{ item.Plot }}</p>
                <p class="card-text"><small class="text-muted">Actors: {{ item.Actors }}</small></p>
                <p class="card-text"><small class="text-muted">Writer: {{ item.Writer }}</small></p>
            </div>
            </div>
        </div>
    </div>
</template>

<script>
import axios from "axios";
export default {
  name: "App",

  data() {
    return {
      item: [],
      imdbID: this.$route.params.id
    };
  },

  methods: {
    async movieData() {
      let result = await axios.get("http://www.omdbapi.com/", {
        params: this.params,
      });
      console.warn(result);
      this.item = result.data;
    }
  },

  computed: {
    params() {
      const params = new URLSearchParams();
      params.append("apikey", "715289b");
      params.append("i", this.imdbID);
      return params;
    },
  },

  mounted() {
    this.movieData();
  },
};
</script>