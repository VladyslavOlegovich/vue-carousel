<template>
  <div id="app">
    <ImageCarousel :images="images" />
  </div>
</template>
<script>
import ImageCarousel from "./components/ImageCarousel.vue";
import DataService from "./services/DataService";

export default {
  components: {
    ImageCarousel,
  },
  data() {
    return {
      images: [],
    };
  },
  mounted() {
    this.fetchImages();
  },
  methods: {
    async fetchImages() {
      try {
        this.images = await DataService.fetchImages();
      } catch (error) {
        console.error("Error fetching images:", error);
      }
    },
  },
};
</script>
<style scoped>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  text-align: center;
  color: #263038;
  background-color: #f5f5dc;
  border-radius: 22px;
}
</style>
