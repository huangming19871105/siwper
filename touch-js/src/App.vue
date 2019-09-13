<template>
  <div id="app">
    <div class="imgWrap">
      <img alt="Vue logo" src="./assets/logo.png" :style="move" />
      <img alt="Vue logo" src="./assets/logo.png"/>
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
      let i = document.querySelectorAll("img")[0];
      console.log(i)
      Touch.bindEvent(i, "swipeleft", this.tLeft);
      Touch.bindEvent(i, "swiperight", this.tRight);
      Touch.bindEvent(i, "slidedown", this.tDown);
      Touch.bindEvent(i, "slideup", this.tUp);
      Touch.bindEvent(i, "move", this.tMove);
      this.$on("hook:destroyed", () => {
        Touch.removeEvent(i, "swipeleft", this.tLeft);
        Touch.removeEvent(i, "swiperight", this.tRight);
      });
    },
    tLeft(e, delta) {
      console.log("left");
      this.resetPosition();
    },
    tRight() {
      console.log("right");
      this.resetPosition();
    },
    tDown() {
      this.resetPosition();
    },
    tUp() {
      this.resetPosition();
    },
    tMove(e, m) {
      this.move = "transform: translate3d(" + -m.x + "px," + 0 + "px,0)";
    },
    resetPosition() {
      this.move = "transform: translate3d(0,0,0)";
    }
  }
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

img {
  transition: all 0.1s;
}

.imgWrap {
  position: relative;
  width: 100%;
  height: 500px;
}
.imgWrap img{
  position: relative;
  z-index: 100;
}
.imgWrap img:nth-child(2){
  position: absolute;
  left: 0;
  right: 0;
  top: 0;
  bottom: 0;
  margin: 0 auto;
  z-index: 50;
}
</style>
