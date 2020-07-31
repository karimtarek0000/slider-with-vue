<template>
  <div class="slider">
    <!-- SLIDER WRAPPER -->
    <div v-on="$listeners" class="slider__wrapper">
      <slot name="allSlides"></slot>
    </div>
    <!-- CONTROLS -->
    <slot></slot>
  </div>
</template>

<script>
export default {
  name: "Sliders"
};
</script>

<style></style>
