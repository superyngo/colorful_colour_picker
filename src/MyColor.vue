<script>
export default {
  data() {
    return {
      rgb: [],
      validRGB: [],
      hex: "",
    };
  },
  props: { modelValue: String },
  emits: ["update:modelValue"],
  methods: {
    hexToRgb(hex) {
      const hexR = hex.substring(1, 3);
      const hexG = hex.substring(3, 5);
      const hexB = hex.substring(5, 7);
      return [parseInt(hexR, 16), parseInt(hexG, 16), parseInt(hexB, 16)];
    },
    rgbToHex(rgb) {
      const hexR = Number(rgb[0]).toString(16).padStart(2, "0");
      const hexG = Number(rgb[1]).toString(16).padStart(2, "0");
      const hexB = Number(rgb[2]).toString(16).padStart(2, "0");
      return `#${hexR}${hexG}${hexB}`;
    },
    submit(hex) {
      //keep first letter #
      if (!/^#([0-9A-F]{0,6})$/i.test(hex)) {
        this.hex = "#" + hex.substring(1, this.hex.length - 1);
      }
      if (/^#([0-9A-F]{6})$/i.test(hex)) {
        this.$emit("update:modelValue", hex);
      }
    },
    validateRGBNumber(e) {
      let value = Number(e.target.value);
      if (Number(value) >= 0 && Number(value) <= 255) {
        this.submit(this.rgbToHex(this.rgb));
        this.validRGB = [...this.rgb];
      } else {
        this.rgb = [...this.validRGB];
      }
    },
    resetValidHex() {
      this.hex = this.modelValue;
    },
    handleInputChange() {
      const barColor = [
        `rgb(${this.rgb[0]}, 0, 0)`,
        `rgb(0, ${this.rgb[1]}, 0)`,
        `rgb(0,0,${this.rgb[2]})`,
      ];
      const contrackBarColor = [
        `rgb(${Math.abs(this.rgb[0] - 255)}, 0, 0)`,
        `rgb(0, ${Math.abs(this.rgb[1] - 255)}, 0)`,
        `rgb(0,0,${Math.abs(this.rgb[2] - 255)})`,
      ];
      this.$refs.rangeInput.forEach((el, index) => {
        const min = el.min;
        const max = el.max;
        const val = el.value;
        el.style.setProperty("--barcolor", barColor[index]);
        //  = `linear-gradient(${barColor[index]}, ${barColor[index]})`;
        el.style.backgroundSize = ((val - min) * 100) / (max - min) + "% 100%";
      });
      this.$refs.wrapper.style.backgroundImage = `conic-gradient(from -90deg,
      ${contrackBarColor[0]} 0%,
      ${contrackBarColor[1]} 33%,
      ${contrackBarColor[2]} 66%,
      ${contrackBarColor[0]} 100%)`;
    },
  },
  mounted() {
    this.rgb = [...this.setRGB];
    this.validRGB = [...this.setRGB];
    this.hex = this.modelValue;
    this.$nextTick(() => {
      this.handleInputChange();
    });
  },
  computed: {
    setRGB() {
      return this.hexToRgb(this.modelValue);
    },
    hexFontColor() {
      return this.rgb[0] * 0.299 + this.rgb[1] * 0.587 + this.rgb[2] * 0.114 >
        186
        ? "black"
        : "white";
    },
  },
  watch: {
    modelValue: {
      handler() {
        this.rgb = [...this.setRGB];
        this.validRGB = [...this.setRGB];
        this.hex = this.modelValue;
        this.$nextTick(() => {
          this.handleInputChange();
        });
      },
    },
  },
};
</script>

<template>
  <!-- <svg>
    <text x="50" y="50" class="text">{{ hex }}</text>
  </svg> -->
  <div class="wrapper" ref="wrapper">
    <div class="hexValue">
      <input
        ref="hexPanel"
        class="hexInput"
        type="text"
        v-model="hex"
        :maxlength="7"
        :style="`color:${hexFontColor};background-Color:${modelValue};`"
        @input="submit(hex)"
        @blur="resetValidHex()"
      />
    </div>
    <div class="RGBinputs" v-for="(i, index) of rgb">
      <span class="rgbLabel" :style="`color:white`">
        {{ ["R", "G", "B"][index] }}
      </span>
      <input
        ref="rangeInput"
        class="rangeInput"
        type="range"
        min="0"
        max="255"
        v-model="rgb[index]"
        @input="submit(rgbToHex(rgb))"
      />
      <input
        class="numberInput"
        v-model="rgb[index]"
        @input="validateRGBNumber($event)"
      />
    </div>
  </div>
</template>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
.wrapper {
  width: 200px;
  /* border: 1px solid rgb(1, 1, 1); */
  box-shadow: 0 0 5px rgba(0, 0, 0);
  border-radius: 4px;
  padding: 15px;
  position: relative;
}
.wrapper:before {
  content: "";
  inset: 0;
  position: absolute;
  z-index: 0;
  background-color: white;
  opacity: 0.2;
}

.hexInput {
  outline: none;
  height: 50px;
  width: 100%;
  text-transform: uppercase;
  text-align: center;
  font-weight: 900;
  margin: 0 0 5px;
  z-index: 1;
  position: relative;
}
.RGBinputs {
  position: relative;
  padding: 5px 0;
  z-index: 1;
  display: flex;
  align-items: center;
}
.rangeInput {
  cursor: pointer;
  --barcolor: transparent;
  border: 1px solid white;
  -webkit-appearance: none;
  width: 110px;
  margin-left: 10px;
  margin-right: 10px;
  height: 5px;
  background: rgba(255, 255, 255, 0.6);
  background-image: linear-gradient(var(--barcolor), var(--barcolor));
  border-radius: 5px;
  background-size: 70% 100%;
  background-repeat: no-repeat;
}

/* Input Thumb */
.rangeInput::-webkit-slider-thumb {
  -webkit-appearance: none;
  height: 15px;
  width: 15px;
  border-radius: 50%;
  background: var(--barcolor);
  cursor: ew-resize;
  box-shadow: 0 0 2px 0 var(--barcolor);
  position: relative;
}
.rangeInput::-webkit-slider-thumb:hover {
  scale: 1.1;
  box-shadow: 0 0 5px 0 var(--barcolor);
}
.rangeInput::-moz-range-thumb {
  -webkit-appearance: none;
  height: 15px;
  width: 15px;
  border-radius: 50%;
  background: var(--barcolor);
  cursor: ew-resize;
  box-shadow: 0 0 2px 0 var(--barcolor);
  position: relative;
}
.rangeInput::-moz-range-thumb:hover {
  scale: 1.1;
  box-shadow: 0 0 5px 0 var(--barcolor);
}
input[type="range"]::-ms-thumb {
  -webkit-appearance: none;
  height: 15px;
  width: 15px;
  border-radius: 50%;
  background: var(--barcolor);
  cursor: ew-resize;
  box-shadow: 0 0 2px 0 var(--barcolor);
  position: relative;
}
.rangeInput::-ms-thumb:hover {
  scale: 1.1;
  box-shadow: 0 0 5px 0 var(--barcolor);
}

.numberInput {
  width: 26px;
  text-align: center;
  overflow: hidden;
}
</style>
