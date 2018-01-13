<template>
    <div class="vue-range-input" :style="{width: `${this.width}px`, 'background-color': this.bgColor}">
        <div class="vue-range-input__line" :style="lineStyle"></div>
        <input type="range" :style="compUpperStyle" class="vue-range-input__upper" @input="upperSliderOnInput" v-model="slider.upperVal" :min="slider.min" :max="slider.max" >
        <input type="range" v-if="initialVal[1]" :style="compLowerStyle" class="vue-range-input__lower" @input="lowerSliderOnInput" v-model="slider.lowerVal" :min="slider.min" :max="slider.max" >
    </div>
</template> 

<script>
export default {
  name: "vue-range-input",
  data() {
    return {
      styleOffset: 24,
      upperStyle: {},
      lowerStyle: {},
      inputCount: 1,
      slider: {
        lowerVal: 0,
        upperVal: 0,
        min: 0,
        max: 0,
      },
    };
  },
  mounted() {},
  computed: {
      compUpperStyle () {
        return {...this.upperStyle, '--round': this.roundColor }
      },
      compLowerStyle () {
        return {...this.lowerStyle, '--round': this.roundColor }
      },
      lineStyleDual() {
          return {
              width: `${this.lineWidthOnlyUpper - this.lineLeftOffset + this.styleOffset}px`,
              left: `${this.styleOffset + this.lineLeftOffset - this.styleOffset/2}px`,
              'background-color': this.color,
              '--color': this.dotColor,
              '--display': 'block'
          }
      },
      lineStyleSolo() {
          return {
              width: `${this.lineWidthOnlyUpper + this.styleOffset/2}px`,
              left: `0`,
              'background-color': this.color,
              '--color': this.dotColor,
              '--display': 'none'
          }
      },
      lineStyle() {
        return this.initialVal[1] ? this.lineStyleDual : this.lineStyleSolo
      },
      lineLeftOffset() {
        return this.indexCalc(this.slider.lowerVal)
      },
      lineWidthOnlyUpper() {
        return this.indexCalc(this.slider.upperVal)
      }
  },
  methods: {
    indexCalc(sliderVal) {
      return ( this.width - this.styleOffset * this.inputCount ) / (this.slider.max - this.slider.min) * (sliderVal - this.slider.min)
    },
    eventDataValues() {
      return this.initialVal[1] ? [parseInt(this.slider.lowerVal),parseInt(this.slider.upperVal)] : parseInt(this.slider.upperVal)
    },
    upperSliderOnInput() {
      if (this.slider.upperVal < parseInt(this.slider.lowerVal)) {
        this.slider.lowerVal = this.slider.upperVal;
      }
      this.$emit('input', this.eventDataValues())
    },
    lowerSliderOnInput() {
      if (this.slider.lowerVal > this.slider.upperVal) {
        this.slider.upperVal = parseInt(this.slider.lowerVal);
      }
      this.$emit('input', this.eventDataValues())
    }
  },
  created() {
      this.slider.min = this.min;
      this.slider.max = this.max;
      this.slider.lowerVal = this.initialVal[0];
      this.inputCount = this.initialVal.length
      if (!this.initialVal[1]) {
        this.slider.upperVal = this.initialVal[0];
        this.upperStyle = { left: `0`, width: `${this.width}px` };
      } else {
        this.slider.upperVal = this.initialVal[1];
        this.upperStyle = {left: `${this.styleOffset}px`, width: `${this.width - this.styleOffset}px`}
        this.lowerStyle = {left: `0`, width: `${this.width - this.styleOffset}px`}
      }
  },
  props: {
    min: Number,
    max: Number,
    initialVal: Array,
    width: Number,
    bgColor: String,
    color: String,
    dotColor: String,
    roundColor: String,
  },
  
};
</script>

<style lang="scss" scoped>
    .vue-range-input {
        &__line {
            top: 0;
            left: 0;
            height: 24px;
            position: absolute;
            &:after, &:before {
              content: ' ';
              position: absolute;
              height: 12px;
              width: 12px;
              border-radius: 12px;
              top: 6px;
              background-color: var(--color);
            }
            &:after {
              right: -6px;
            }
            &:before {
              display: var(--display);
              left: -6px;
            }
        }
        overflow: hidden;
        position: relative;
        height: 24px;
        border-radius: 24px;
    }

input[type=range] {
  -webkit-box-sizing: border-box;
          box-sizing: border-box;
  -webkit-appearance: none;
     -moz-appearance: none;
          appearance: none;
  overflow: hidden;
  height: 24px;
  margin: 0;
  padding: 0;
  /* Add some L/R padding to ensure box shadow of handle is shown */
  border: 0;
  border-radius: 1px;
  outline: none;
  pointer-events: none;
  position: absolute;
  top: 50%;
  left: 0;
  -webkit-transform: translateY(-50%);
          transform: translateY(-50%);
  background: none;
}
input[type=range]:active,
input[type=range]:focus {
  outline: none;
}

input[type=range]::-webkit-slider-thumb {
    height: 24px;
    width: 24px;
    border-radius: 24px;
    background-color: transparent;
    position: relative;
    /* Add some margin to ensure box shadow is shown */
    cursor: pointer;
    -webkit-appearance: none;
            appearance: none;
    pointer-events: all;
    border-width: 6px;
    border-style: solid;
    border-color: var(--round); 
    
}

</style>
