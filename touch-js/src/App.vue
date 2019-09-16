<template>
  <div id="app">
    <div class="imgWrap">
      <div class="it" :style="move"></div>
      <div class="it"></div>
    </div>
    
  </div>
</template>

<script>
import HelloWorld from "./components/HelloWorld.vue";
import Touch from "./utils/touch";

export default {
  name: "app",
  data() {
    return {
      move: ""
    };
  },
  components: {
    HelloWorld
  },
  mounted() {
    this.bindTouch();
  },
  methods: {
    bindTouch() {
      let i = document.querySelectorAll(".it")[0];
      console.log(i)
      Touch.bindEvent(i, "swipeleft", this.tLeft);
      Touch.bindEvent(i, "swiperight", this.tRight);
      Touch.bindEvent(i, "def", this.resetPosition);
      Touch.bindEvent(i, "move", this.tMove);
      this.$on("hook:destroyed", () => {
        Touch.removeEvent(i, "swipeleft", this.tLeft);
        Touch.removeEvent(i, "swiperight", this.tRight);
      });
    },
    tLeft(e, delta) {
      console.log("left");
    },
    tRight() {
      console.log("right");
    },
    tMove(e, m) {
      this.move = "transform: translate3d(" + -m.x + "px," + 0 + "px,0);transition: all 0.1s;";
    },
    resetPosition() {
      this.move = "transform: translate3d(0,0,0);transition: all 0.1s;";
    }
  }
};
</script>

<style>
*{
  margin: 0;
  padding: 0;
}
body{
  overflow: hidden;
}
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.imgWrap {
  position: relative;
  width: 100%;
  height: 300px;
}
.imgWrap .it{
  position: relative;
  margin: 0 auto;
  width: 300px;
  height: 300px;
  background: red;
  z-index: 100;
}
.imgWrap .it:nth-child(2){
  position: absolute;
  left: 0;
  right: 0;
  top: 0;
  bottom: 0;
  margin: 0 auto;
  z-index: 50;
  background: green;
}
</style>
