<template>
  <div class="image-carousel">
    <div v-if="images.length" class="carousel-container">
      <transition name="fade" mode="out-in">
        <div class="horizontal-carousel" :key="currentIndex">
          <img
            v-for="(image, index) in visibleImages"
            :src="image.download_url"
            class="carousel-image"
            :alt="image.author"
            :key="index"
            @click="toggleSelection(image)"
            :class="{ selected: image.selected }"
          />
        </div>
      </transition>
      <button @click="prev" class="prev-button">&#9665;</button>
      <button @click="next" class="next-button">&#9655;</button>
    </div>

    <div class="selected-images">
      <div
        v-for="(image, index) in selectedImages"
        :key="index"
        class="selected-image"
      >
        <a target="_blank" :href="image">{{ image }}</a>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    images: Array,
  },
  data() {
    return {
      currentIndex: 0,
      selectedImages: [],
      desktopVisibleCount: 5,
      imagesWithSelection: [],
    };
  },
  computed: {
    visibleImages() {
      this.imagesWithSelection = this.images.map((image) => ({
        ...image,
        selected: this.selectedImages.includes(image.download_url),
      }));

      const start = this.currentIndex;
      const end =
        (start + this.desktopVisibleCount) % this.imagesWithSelection.length;

      return this.imagesWithSelection
        .slice(start, end)
        .concat(
          this.imagesWithSelection.slice(
            0,
            end < start ? this.desktopVisibleCount : 0
          )
        );
    },
  },
  created() {
    this.handleResize();
  },
  mounted() {
    window.addEventListener("resize", this.handleResize);
  },
  beforeDestroy() {
    window.removeEventListener("resize", this.handleResize);
  },
  methods: {
    prev() {
      this.currentIndex =
        (this.currentIndex - 1 + this.images.length) % this.images.length;
    },
    next() {
      this.currentIndex = (this.currentIndex + 1) % this.images.length;
    },
    removeSelectedImage(index) {
      this.selectedImages.splice(index, 1);
    },
    handleResize() {
      if (window.innerWidth >= 900) {
        this.desktopVisibleCount = 5;
      } else if (window.innerWidth >= 768) {
        this.desktopVisibleCount = 3;
      } else {
        this.desktopVisibleCount = 1;
      }

      this.currentIndex = 0;
    },

    toggleSelection(image) {
      if (image.selected) {
        this.selectedImages = this.selectedImages.filter(
          (selectedImage) => selectedImage !== image.download_url
        );
      } else {
        this.selectedImages.push(image.download_url);
      }

      image.selected = !image.selected;
    },
  },
};
</script>

<style scoped>
.image-carousel {
  text-align: center;
  padding: 1rem;
}
.carousel-container {
  position: relative;
  overflow: hidden;
  display: inline-block;
}
.horizontal-carousel {
  max-width: 100%;
  display: flex;
}
.carousel-image {
  width: calc(100% / 5);
  height: 250px;
  transition: opacity 0.5s ease-in-out, transform 0.5s ease-in-out,
    box-shadow 0.5s ease-in-out;
  border: 4px solid transparent;
  border-radius: 5px;
  object-fit: cover;
  opacity: 1;
}
.carousel-image:hover {
  cursor: pointer;
  animation: shadowFadeIn 0.5s ease-in-out;
  box-shadow: rgba(136, 165, 191, 0.48) 6px 2px 16px 0px,
    rgba(255, 255, 255, 0.8) -6px -2px 16px 0px;
}
@keyframes shadowFadeIn {
  from {
    box-shadow: rgba(136, 165, 191, 0) 6px 2px 16px 0px,
      rgba(255, 255, 255, 0) -6px -2px 16px 0px;
  }
  to {
    box-shadow: rgba(136, 165, 191, 0.48) 6px 2px 16px 0px,
      rgba(255, 255, 255, 0.8) -6px -2px 16px 0px;
  }
}
.carousel-image.selected {
  opacity: 0.8;
}
@media (max-width: 991px) {
  .carousel-image {
    width: calc(100% / 3);
    height: 250px;
  }
}
@media (max-width: 767px) {
  .carousel-image {
    width: calc(100% / 1);
    height: 250px;
  }
}
.prev-button,
.next-button {
  position: absolute;
  top: 50%;
  padding: 0.6em 1.2em;
  border-radius: 8px;
  font-size: 1em;
  transform: translateY(-50%);
  font-size: 24px;
  background: transparent;
  border: none;
  cursor: pointer;
  color: #333;
  outline: 4px solid transparent;
  transition: outline 0.8s ease;
}

button:hover {
  outline: 2px auto -webkit-focus-ring-color;
}
.prev-button {
  left: 10px;
}
.next-button {
  right: 10px;
}
.selected-images {
  margin-top: 10px;
}
.selected-image {
  margin: 0 5px;
  cursor: pointer;
  transition: opacity 0.5s ease-in-out;
  animation: fadeIn 0.5s ease-in-out;
}
@keyframes fadeIn {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}
.selected-image img {
  width: 50px;
  height: 50px;
  border-radius: 50%;
  object-fit: cover;
}
.selected-image a {
  text-decoration: none;
  color: #3498db;
  font-weight: bold;
  opacity: 1;
  transition: opacity 0.9s ease-in-out;
}
.selected-image a:hover {
  color: #e74c3c;
  animation: hoverFadeIn 0.5s ease-in-out;
}
@keyframes hoverFadeIn {
  from {
    color: #3498db;
  }
  to {
    color: #e74c3c;
  }
}
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.7s;
}
.fade-enter,
.fade-leave-to {
  opacity: 0.8;
}
</style>
