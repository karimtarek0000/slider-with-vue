<template>
  <LogicSlider
    :data="dataBase"
    :slidePerPage="3"
    :statusSliderAuto="true"
    :timeUpdateSlide="10"
    v-model="startIndecators"
  >
    <!-- SLIDERS -->
    <Sliders v-on="sliderEvent" slot-scope="{ allData, countPage, sliderEvent }">
      <!-- SLIDER -->
      <template slot="allSlides" v-for="(slide, index) in allData">
        <div class="slide" :key="index">
          <!-- SLIDE IMAGE -->
          <div class="slide__image">
            <lazyImage :srcImg="slide.img" />
          </div>
          <!-- SLIDE INFO -->
          <div class="slide__info">
            <p class="slide__title">{{ slide.title }}</p>
          </div>
        </div>
      </template>
      <!-- CONTROL -->
      <Control v-model="startIndecators" :countIndecators="countPage" />
    </Sliders>
  </LogicSlider>
</template>

<script>
//
import MixinSlider from "@/components/slider/MixinSlider.js";
//
export default {
  name: "Slider",
  mixins: [MixinSlider],
  data() {
    return {
      startIndecators: 1,
      dataBase: [
        {
          title: "slide-1",
          img: "https://source.unsplash.com/1920x1080/?computer"
        },
        {
          title: "slide-2",
          img: "https://source.unsplash.com/1920x1080/?cars"
        },
        {
          title: "slide-3",
          img: "https://source.unsplash.com/1920x1080/?woman"
        },
        {
          title: "slide-4",
          img: "https://source.unsplash.com/1920x1080/?water"
        },
        {
          title: "slide-5",
          img: "https://source.unsplash.com/1920x1080/?girls"
        },
        {
          title: "slide-6",
          img: "https://source.unsplash.com/1920x1080/?men"
        },
        {
          title: "slide-7",
          img: "https://source.unsplash.com/1920x1080/?car"
        },
        {
          title: "slide-8",
          img: "https://source.unsplash.com/1920x1080/?car"
        },
        {
          title: "slide-9",
          img: "https://source.unsplash.com/1920x1080/?car"
        },
        {
          title: "slide-10",
          img: "https://source.unsplash.com/1920x1080/?car"
        },
        {
          title: "slide-11",
          img: "https://source.unsplash.com/1920x1080/?car"
        },
        {
          title: "slide-12",
          img: "https://source.unsplash.com/1920x1080/?car"
        },
        {
          title: "slide-13",
          img: "https://source.unsplash.com/1920x1080/?car"
        },
        {
          title: "slide-14",
          img: "https://source.unsplash.com/1920x1080/?car"
        }
      ]
    };
  }
};
</script>

<style lang="scss">
// SLIDE
.slide {
  position: relative;
  background-color: white;
  color: white;
  margin: 0 3px;
  overflow: hidden;
  user-select: none;
  // INFO
  &__info {
    position: absolute;
    bottom: 0;
    left: 0;
    padding: 10px;
    text-align: left;
    text-transform: capitalize;
  }
  // IMAGE
  &__image {
    width: 100%;
    height: 100%;
    // IMG
    img {
      max-width: 100%;
      width: 100%;
      height: 100%;
      object-fit: cover;
    }
    // AFTER
    &::after {
      content: "";
      position: absolute;
      background-image: linear-gradient(to top, rgba(black, 0.8) 30%, rgba(black, 0.2));
      width: 100%;
      height: 100%;
      top: 0;
      left: 0;
    }
  }
  // TITLE
  &__title {
    font-size: 40px;
  }
  // NAME
  &__subtitle {
    font-size: 20px;
    margin-bottom: 5px;
  }
  // DESCRIPTION
  &__description {
    font-size: 17px;
  }
}
</style>
