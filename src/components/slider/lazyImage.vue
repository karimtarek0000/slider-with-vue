<template>
  <img class="image" :src="statusInterSecting" />
</template>

<script>
export default {
  name: "lazyImage",
  props: {
    srcImg: {
      type: String,
      default: ""
    }
  },
  data() {
    return {
      interSecting: false,
      observer: null
    };
  },
  computed: {
    // IF INTERSECTING TRUE WILL BE ADD IMAG INTO SRC
    statusInterSecting() {
      return this.interSecting ? this.srcImg : "";
    }
  },
  watch: {
    // WATCH THE INTERSECTION
    interSecting(n) {
      if (n) this.$el.style.setProperty("opacity", 1);
    }
  },
  mounted() {
    // INIT INTER SECTION OBSERVER
    this.observer = new IntersectionObserver(entries => {
      // SET IMAGE IN VARIABLE
      const image = entries[0];
      if (image.isIntersecting) {
        // WILL BE SET INTERSECTING TO TRUE
        this.interSecting = true;
        // NOT WATCH ANY MORE
        this.observer.disconnect();
      }
    });
    // INIT
    this.observer.observe(this.$el);
  },
  destroyed() {
    // NOT WATCH ANY MORE
    this.observer.disconnect();
  }
};
</script>

<style scoped>
/*  */
.image {
  opacity: 0;
  transition: opacity 1.5s ease-out;
}
/*  */
img {
  max-width: 100%;
  width: 100%;
  height: 100%;
  object-fit: cover;
}
</style>
