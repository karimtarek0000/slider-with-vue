<script>
export default {
  name: "LogicSlider",
  model: {
    prop: "numIndecators",
    event: "updateNumIndecators"
  },
  props: {
    data: {
      type: Array,
      required: true
    },
    slidePerPage: {
      type: [String, Number],
      default: 1
    },
    numIndecators: {
      type: Number,
      default: 1
    },
    statusSliderAuto: {
      type: Boolean,
      default: false
    },
    timeUpdateSlide: {
      type: Number,
      default: 5
    }
  },
  data() {
    return {
      countSlidePage: this.slidePerPage,
      //////////////
      coordMove: {
        start: 0,
        end: 0
      },
      //////////////
      status: false,
      //////////////
      allPagesSwip: [],
      countPageSwip: 0,
      countSwip: this.numIndecators,
      //////////////
      countSlideAuto: this.numIndecators,
      interval: null,
      statusSliderAutoLocal: this.statusSliderAuto
    };
  },
  computed: {
    // LENGTH DATA
    lengthData() {
      return this.data.length;
    },
    // COUNT PAGE
    countPage() {
      return Math.ceil(this.lengthData / this.countSlidePage);
    },
    // GET ELEMENT
    getElement() {
      const slider = this.$el;
      const wrapper =
        slider.children[Array.from(this.$el.children).findIndex(el => el.classList.contains("slider__wrapper"))];
      const sliders = wrapper.children;

      //
      return {
        slider,
        wrapper,
        sliders
      };
    },
    // STATUS COORD
    statusCoord() {
      return this.coordMove.start !== 0 && this.coordMove.end !== 0;
    },
    // CONVERT TO SECONDS
    convertToSeconds() {
      return this.timeUpdateSlide * 1000;
    }
  },
  methods: {
    ///////////////////////////////
    // CALC WIDTH SLIDE
    calcWidthSlide() {
      // SET WIDTH
      const setWidthSlide = this.getElement.slider.clientWidth / this.countSlidePage + "px";
      const setWidthWrapper = (this.getElement.slider.clientWidth / this.countSlidePage) * this.lengthData + "px";
      // SET WIDTH SLIDER WRAPPER
      this.getElement.wrapper.style.width = setWidthWrapper;
      // SET WIDTH ALL SLIDE
      this.getElement.sliders.forEach(slide => (slide.style.width = setWidthSlide));
    },
    // MOVE SLIDER
    moveSlider() {
      // SET CALC MOVE
      const _move = this.getElement.slider.clientWidth * (this.numIndecators - 1);
      // WRAPPER ELEMENT WILL BE CHANGE STYLE TRANSLATE
      this.animationSlider(_move);
    },
    // ANIMATION SLIDER
    animationSlider(move) {
      return (this.getElement.wrapper.style.transform = `translateX(-${move}px)`);
    },
    ///////////////////////////////
    // DRUING EFFECTS
    dragingEffects() {
      const maxDistance = 2;
      const calcMove =
        this.getElement.slider.clientWidth * this.countPageSwip +
        (this.coordMove.start - this.coordMove.end) / maxDistance;

      //
      this.animationSlider(calcMove);
    },
    // PREPARE COUNT SCREEN
    prepareCountSwip() {
      //
      if (this.numIndecators > 1) this.countPageSwip = this.numIndecators - 1;
      //
      for (var i = 0; i < this.countPage; i++) {
        if (this.allPagesSwip[i] !== i) {
          this.allPagesSwip.push(i);
        }
      }
    },
    // RULE MOVE SLIDE DRUGING
    ruleMoveSliderDruging() {
      // IF STATUS COORD
      if (this.statusCoord) {
        //
        const totalTouchDrag = Math.abs(this.coordMove.start - this.coordMove.end);
        const maxDistance = this.getElement.slider.clientWidth / 3;
        const calcSlider = this.getElement.slider.clientWidth * this.countPageSwip;
        //
        if (totalTouchDrag < maxDistance) {
          this.animationSlider(calcSlider);
          return false;
        } else {
          if (this.coordMove.start > this.coordMove.end) {
            //
            this.countPageSwip = this.allPagesSwip[++this.countPageSwip];
            // UPDATE NUM INDECATORS
            this.$emit("updateNumIndecators", ++this.countSwip);
          } else if (this.coordMove.start < this.coordMove.end) {
            //
            this.countPageSwip = this.allPagesSwip[--this.countPageSwip];
            // UPDATE NUM INDECATORS
            this.$emit("updateNumIndecators", --this.countSwip);
          }
        }
      }
    },
    ///////////////////////////////
    // RESPONSIVE WIDTH
    responsiveWidth() {
      // VAR WINDOW WIDTH
      const windowWidth = window.innerWidth;
      // IF SLIDER PER PAGE GRATER THAN 1
      if (this.slidePerPage > 1) {
        // WIDTH LESS THAN 600
        if (windowWidth <= 600) {
          this.countSlidePage = 1;
          // WIDTH GREATER THAN 600 AND LESS THANE OR EQUAL 800
        } else if (windowWidth > 600 && windowWidth <= 800) {
          this.countSlidePage = 2;
          // WIDTH GREATER THAN 800 AND LESS THANE OR EQUAL 1000
        } else if (windowWidth > 800 && windowWidth <= 1000 && this.slidePerPage > 3) {
          this.countSlidePage = 3;
        } else {
          // STILL SAME NUMBER SLIDER PER PAGE
          this.countSlidePage = this.slidePerPage;
        }
      }
      // CALC WIDTH SLIDE
      this.calcWidthSlide();
    },
    // MOVE SLIDER AUTO
    moveSliderAuto() {
      // SET INTERVAL
      if (this.statusSliderAutoLocal) {
        if (this.countSlideAuto == this.countPage) {
          // UPDATE INDECATORS
          this.$emit("updateNumIndecators", 1);
          // SET COUNT SLIDE AUTO EQUAL 1
          this.countSlideAuto = 1;
        } else {
          // UPDATE SWIP IF CHANGE SLIDE WITH SWIP WILL BE WATCH ALL UPDATE
          this.countPageSwip = this.allPagesSwip[++this.countSlideAuto];
          // UPDATE INDECATORS
          this.$emit("updateNumIndecators", this.countSlideAuto);
        }
        // INCREMENT COUNT SLIDE AUTO
        ++this.countSlideAuto;
      }

      // WILL BE CLEAR INTERVAL
      if (!this.statusSliderAuto) {
        // CLEAR INTERVAL
        clearInterval(this.interval);
      }
    }
  },
  watch: {
    // NUM INDECATORS
    numIndecators(newNum) {
      // RUN MOVE SLIDER
      this.moveSlider();
      // IF NEW NUM GREATER THAN OR EQUAL COUNT PAGE WILL BE RETURN CUSTOM EVENT EQUAL COUNT PAGE
      if (newNum >= this.countPage) {
        this.$emit("updateNumIndecators", this.countPage);
        this.countPageSwip = this.allPagesSwip[this.allPagesSwip.length - 1];
        this.countSwip = this.countPage;
        // IF NEW NUM LESS THAN OR EQUAL 1 WILL BE RETURN CUSTOM EVENT EQUAL 1
      } else if (newNum <= 1) {
        this.$emit("updateNumIndecators", 1);
        this.countPageSwip = this.allPagesSwip[0];
        this.countSwip = 1;
      }
      // SET COUNT SWIP EQUAL NEW NUM
      this.countSwip = newNum;
      this.countSlideAuto = newNum;
      // SCREEN SWIP
      if (newNum > this.countPageSwip || this.countPageSwip > newNum || newNum == this.countPageSwip)
        this.countPageSwip = this.allPagesSwip[--newNum];
    }
  },
  mounted() {
    // RUN CALC WIDTH SLIDE
    this.responsiveWidth();
    // ADD EVENT LISTENER => RESIZE
    window.addEventListener("resize", () => {
      // RUN CALC WIDTH SLIDE
      this.responsiveWidth();
      // CUSTOM EVENT RETURN 1
      this.$emit("updateNumIndecators", 1);
      // PREPARE COUNT SWIP
      this.prepareCountSwip();
    });
    // RUN MOVE SLIDER
    this.moveSlider();
    // PREPARE COUNT SWIP
    this.prepareCountSwip();
    // SET INTERVAL WILL BE RUN AUT SLIDER
    this.interval = setInterval(this.moveSliderAuto, this.convertToSeconds);
  },
  destroyed() {
    // CLEAR INTERVAL
    clearInterval(this.interval);
  },
  render() {
    return this.$scopedSlots.default({
      allData: this.data,
      countPage: this.countPage,
      /////////////////////////////////////
      sliderEvent: {
        // MOUSE DOWN
        mousedown: e => {
          // SWITCH STATUS => TRUE
          this.status = true;
          // START EQUAL PAGE X
          this.coordMove.start = e.pageX;
        },
        // MOUSE UP
        mouseup: e => {
          //
          e.preventDefault();
          // SWITCH STATUS => FALSE
          this.status = false;
          // RUN MOVE SLIDER DRUGING
          this.ruleMoveSliderDruging();
        },
        // MOUSE MOVE
        mousemove: e => {
          // STATUS SLIDER AUTO LOCAL EQUAL FALSE
          this.statusSliderAutoLocal = false;
          // IF STATUS FALSE WILL BE RETURN FALSE
          if (!this.status) return;
          // END EQUAL PAGE X
          this.coordMove.end = e.pageX;
          // RUN FN DRAGING EFFECTS
          this.dragingEffects();
        },
        // MOUSE LEAVE
        mouseleave: () => {
          // STATUS SLIDER AUTO LOCAL EQUAL TRUE
          this.statusSliderAutoLocal = true;
          // SWITCH STATUS => FALSE
          this.status = false;
          // SWITCH START AND END EQUAL 0
          [this.coordMove.start, this.coordMove.end] = [0, 0];
        },
        // TOUCH START
        touchstart: e => {
          // SWITCH STATUS => TRUE
          this.status = true;
          this.coordMove.start = e.touches[0].pageX;
        },
        // TOUCH END
        touchend: () => {
          // STATUS SLIDER AUTO LOCAL EQUAL FALSE
          this.statusSliderAutoLocal = true;
          // SWITCH STATUS => FALSE
          this.status = false;
          // RUN MOVE SLIDER DRUGING
          this.ruleMoveSliderDruging();
        },
        // TOUCH MOVE
        touchmove: e => {
          if (e.cancelable) {
            // STATUS SLIDER AUTO LOCAL EQUAL FALSE
            this.statusSliderAutoLocal = false;
            // IF STATUS FALSE WILL BE RETURN FALSE
            if (!this.status) return;
            // END EQUAL PAGE X
            this.coordMove.end = e.touches[0].pageX;
            // RUN FN DRAGING EFFECTS
            this.dragingEffects();
          }
        },
        // TOUCH LEAVE
        touchleave: () => {
          // STATUS SLIDER AUTO LOCAL EQUAL FALSE
          this.statusSliderAutoLocal = true;
          // SWITCH STATUS => FALSE
          this.status = false;
          // SWITCH START AND END EQUAL 0
          [this.coordMove.start, this.coordMove.end] = [0, 0];
        }
      }
    });
  }
};
</script>

<style lang="scss">
//  SLIDER
.slider {
  position: relative;
  display: flex;
  flex-direction: column;
  width: 100%;
  height: 90vh;
  background-color: transparent;
  overflow: hidden;

  // WRAPPER
  &__wrapper {
    display: flex;
    flex-flow: nowrap row;
    background-color: inherit;
    flex-grow: 1;
    overflow: hidden;
    transition: transform 0.3s cubic-bezier(0.26, 0.23, 0.12, 0.76);
  }
}
// CONTROL
.control {
  display: flex;
  justify-content: space-around;
  align-items: center;
  width: 40%;
  height: 50px;
  margin-left: auto;
  background-color: transparent;
  overflow: hidden;

  // RESPONSIVE
  @media only screen and (max-width: 900px) {
    width: 60%;
  }
  @media only screen and (max-width: 600px) {
    justify-content: center;
    width: 100%;
  }
  // ALL INDECATORS
  &__all-indecators {
    display: flex;
    list-style: none;
    max-width: 100%;
  }
  // INDECATORS
  &__indecators {
    position: relative;
    display: flex;
    justify-content: center;
    align-items: center;
    width: 20px;
    height: 20px;
    border-radius: 50%;
    border: 1px solid white;
    cursor: pointer;
    // RESPONSIVE
    @media only screen and (max-width: 600px) {
      width: 15px;
      height: 15px;
    }
    // AFTER
    &::after {
      content: "";
      position: absolute;
      width: 10px;
      height: 10px;
      background-color: white;
      border-radius: 50%;
      opacity: 0;
      transition: opacity 0.2s ease;
      // RESPONSIVE
      @media only screen and (max-width: 600px) {
        width: 7px;
        height: 7px;
      }
    }

    // ACTIVE
    &--active {
      // AFTER
      &::after {
        opacity: 1;
      }
    }
    // NOT - LAST CHILD
    &:not(:last-child) {
      margin-right: 10px;
    }
  }
  // ARROW
  &__arrow {
    fill: white;
    width: 30px;
    height: 50px;
    cursor: pointer;

    @media only screen and (max-width: 600px) {
      display: none;
    }
    //
    &:not(:first-child) {
      margin-left: 20px;
    }
    // RIGHT
    &--right {
    }
    &--left {
    }

    &--hide {
      pointer-events: none;
      opacity: 0.5;
    }
  }
}
</style>
