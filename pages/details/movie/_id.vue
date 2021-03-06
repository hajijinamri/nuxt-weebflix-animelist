<template>
  <div id="anime-details">
    <div class="video-player mx-auto" v-if="res.trailer_url != null">
      <iframe :src="res.trailer_url"> </iframe>
    </div>
    <div :class="dynamicStyle">
      <div class="md:flex md:text-left text-center">
        <Spinner v-if="isLoading" class="ml-8 md:ml-0" />
        <img
          :src="res.image_url"
          alt="Poster"
          class="rounded shadow-lg details-poster mx-auto md:mx-0"
          v-else
        />
        <div class="details md:ml-6 md:mt-0 mt-5">
          <h2 class="text-2xl font-semibold">
            <span v-if="res.status == 'Not yet aired'">{{ res.status }}</span>
            <span class="text-yellow-500" v-else> ★ </span> {{ res.score }}
          </h2>
          <div class="head-title md:flex">
            <h1 class="text-3xl md:text-4xl font-semibold">{{ res.title }}</h1>
          </div>
          <p class="text-lg">
            <span class="font-medium">Release : </span>{{ this.date }}
          </p>
          <p class="text-lg">
            <span class="font-medium">Genre : </span>
            <template v-for="res in res.genres">{{ res.name }}, </template>
          </p>
          <p class="text-lg">
            <span class="font-medium">Duration : </span>{{ res.duration }}
          </p>
          <p class="text-lg">
            <span class="font-medium">Studio : </span>
            <template v-for="res in res.studios">{{ res.name }} </template>
          </p>
        </div>
      </div>
    </div>
    <div class="container mx-auto mt-16 px-5 md:px-10 mb-16">
      <div class="synopsis">
        <h1 class="md:text-4xl text-3xl mb-4">Synopsis</h1>
        <p>{{ res.synopsis }}</p>
      </div>
      <div class="recommendations mt-20">
        <h1 class="md:text-4xl text-3xl mb-4">Recommendations</h1>
        <div
          class="grid grid-cols-2 md:grid-cols-4 lg:grid-cols-5 gap-6"
          v-if="recommendedAnime.length > 1"
        >
          <anime-list
            v-for="result in recommendedAnime"
            :key="result.mal_id"
            :id="result.mal_id"
            :poster="result.image_url"
            :title="result.title"
          />
        </div>
        <h2 class="md:text-xl text-base mb-4" v-else>
          No recommendation available.
        </h2>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  layout: "details",
  data() {
    return {
      res: "",
      date: "",
      isLoading: false,
      recommendedAnime: "",
    };
  },
  head() {
    return {
      title: `${this.res.title} - Weeblix`,
      meta: [
        { charset: "utf-8" },
        { name: "viewport", content: "width=device-width, initial-scale=1" },
        {
          hid: "description",
          name: "description",
          content: this.res.synopsis,
        },
      ],
    };
  },
  computed: {
    dynamicStyle() {
      //minus margin top will be removed if there's no trailer
      if (this.res.trailer_url != null) {
        return "container px-5 md:w-9/12 mx-auto mt-12 md:-mt-16 main-details";
      }
      return "container px-5 md:w-9/12 mx-auto mt-12";
    },
  },
  methods: {
    async getDetails() {
      this.isLoading = true;
      try {
        const res = await fetch(
          `https://api.jikan.moe/v3/anime/${this.$route.params.id}`
        );
        this.res = await res.json();
        this.date = this.res.aired.string;

        this.isLoading = false;
        // console.log(this.res);
      } catch (err) {
        console.log(err);
      }
    },
    async getRecommendations() {
      try {
        const res = await fetch(
          `https://api.jikan.moe/v3/anime/${this.$route.params.id}/recommendations`
        );
        const data = await res.json();
        this.recommendedAnime = data.recommendations.slice(0, 20);

        // console.log(this.recommendedAnime);
      } catch (err) {
        console.log(err);
      }
    },
  },
  created() {
    this.getDetails();
    this.getRecommendations();
  },
};
</script>