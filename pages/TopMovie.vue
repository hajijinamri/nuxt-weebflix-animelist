<template>
  <div class="movies">
    <h1 class="md:text-4xl text-3xl mb-4">Top Anime Movies</h1>
    <div class="grid grid-cols-2 md:grid-cols-4 lg:grid-cols-5 gap-6">
      <anime-list
        v-for="result in $store.state.topMovie"
        :key="result.mal_id"
        :id="result.mal_id"
        :poster="result.image_url"
        :score="result.score"
        :title="result.title"
        :type="result.type"
      />
    </div>
  </div>
</template>

<script>
export default {
  created() {
    this.$store.dispatch('getTopMovie')
  }
}
</script>