<template>
  <div class="control">
    <!-- INDECATORS -->
    <ul class="control__all-indecators">
      <li
        v-for="indecators in countIndecators"
        :key="indecators"
        @click="updateMoveSlider(indecators)"
        :class="['control__indecators', { 'control__indecators--active': indecators == numIndecators }]"
      ></li>
    </ul>
    <!-- ARROWS -->
    <div class="control__arrows">
      <svg
        :class="['control__arrow control__arrow', { 'control__arrow control__arrow--hide': statusHideArrowLeft }]"
        version="1.1"
        xmlns="http://www.w3.org/2000/svg"
        viewBox="0 0 11 28"
        @click="updateMoveSlider(--index)"
      >
        <title>move left</title>
        <path
          d="M9.797 8.5c0 0.125-0.063 0.266-0.156 0.359l-6.141 6.141 6.141 6.141c0.094 0.094 0.156 0.234 0.156 0.359s-0.063 0.266-0.156 0.359l-0.781 0.781c-0.094 0.094-0.234 0.156-0.359 0.156s-0.266-0.063-0.359-0.156l-7.281-7.281c-0.094-0.094-0.156-0.234-0.156-0.359s0.063-0.266 0.156-0.359l7.281-7.281c0.094-0.094 0.234-0.156 0.359-0.156s0.266 0.063 0.359 0.156l0.781 0.781c0.094 0.094 0.156 0.219 0.156 0.359z"
        ></path>
      </svg>
      <svg
        :class="['control__arrow control__arrow', { 'control__arrow control__arrow--hide': statusHideArrowRight }]"
        version="1.1"
        xmlns="http://www.w3.org/2000/svg"
        viewBox="0 0 9 28"
        @click="updateMoveSlider(++index)"
      >
        <title>move right</title>
        <path
          d="M9.297 15c0 0.125-0.063 0.266-0.156 0.359l-7.281 7.281c-0.094 0.094-0.234 0.156-0.359 0.156s-0.266-0.063-0.359-0.156l-0.781-0.781c-0.094-0.094-0.156-0.219-0.156-0.359 0-0.125 0.063-0.266 0.156-0.359l6.141-6.141-6.141-6.141c-0.094-0.094-0.156-0.234-0.156-0.359s0.063-0.266 0.156-0.359l0.781-0.781c0.094-0.094 0.234-0.156 0.359-0.156s0.266 0.063 0.359 0.156l7.281 7.281c0.094 0.094 0.156 0.234 0.156 0.359z"
        ></path>
      </svg>
    </div>
  </div>
</template>

<script>
export default {
  name: "Control",
  model: {
    prop: "numIndecators",
    event: "sendNumIndecators"
  },
  props: {
    countIndecators: {
      type: Number,
      required: true
    },
    numIndecators: {
      type: Number,
      required: true,
      validator(value) {
        return value > 0;
      }
    }
  },
  data() {
    return {
      index: this.numIndecators
    };
  },
  computed: {
    // STATUS HIDE ARROW RIGHT
    statusHideArrowRight() {
      return this.numIndecators == this.countIndecators;
    },
    // STATUS HIDE ARROW LEFT
    statusHideArrowLeft() {
      return this.numIndecators == 1;
    }
  },
  watch: {
    // INDEX
    index(newNum) {
      if (newNum > this.numIndecators || newNum < this.numIndecators) {
        this.index = this.numIndecators;
      }
    },
    // NUM INDECATORS
    numIndecators(newNum) {
      this.index = newNum;
    }
  },
  methods: {
    // UPDATE MOVE SLIDER
    updateMoveSlider(indecators) {
      this.$emit("sendNumIndecators", indecators);
    }
  }
};
</script>

<style></style>
