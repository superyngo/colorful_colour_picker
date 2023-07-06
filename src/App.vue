<script>
import qColor from "./MyColor.vue";
export default {
  components: {qColor},
  data() {
    return {
      hex: "#010188",
    };
  },
  methods: {},
  watch: {},
  computed: {},
  mounted() {},
};
</script>

<template>
  <div class="container">
    <!-- <button @click="hex = '#FFFFFF'">test</button> -->

    <div>
      <q-color v-model="hex" />
    </div>
  </div>
</template>

<style>
:root {
  font-size: 16px;
}
.background {
  background-color: rgba(127, 255, 212, 0.427);
  position: absolute;
  inset: 0;
  z-index: -1;
}
.img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  filter: opacity(0.5);
}
.appWrap {
  min-height: 100svh;
  display: grid;
  place-content: center;
}
* {
  margin: 0;
  padding: 0;
}
.body {
  min-height: 100svh;
}
.container {
  padding: 10px;
}
.container > * {
  margin: 10px;
}
</style>
