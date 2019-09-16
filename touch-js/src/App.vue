<template>
  <div id="app">
    <div class="imgWrap">
      <div class="it" v-for="(item, index) in data" :style="index == 0 ? move : ''">{{item}}</div>
    </div>
  </div>
</template>

<script>
import Touch from "./utils/touch";

export default {
  name: "app",
  data() {
    return {
      move: "",
      data: [1,2]
    };
  },
  mounted() {
    this.bindTouch();
  },
  methods: {
    bindTouch() {
      let _this = this;
      let i = document.querySelectorAll(".it")[0];
      Touch.bindEvent(i, "swipeleft", this.tLeft);
      Touch.bindEvent(i, "swiperight", this.tRight);
      Touch.bindEvent(i, "slideup", this.resetPosition);
      Touch.bindEvent(i, "slidedown", this.resetPosition);
      Touch.bindEvent(i, "move", this.tMove);
      
      _this.$on("hook:destroyed", () => {
        Touch.removeEvent(i, "swipeleft", this.tLeft);
        Touch.removeEvent(i, "swiperight", this.tRight);
      });
    },
    tLeft(e, delta) {
      var _this = this;
      if(delta.x > 130){
        _this.resetPosition2(function(){
          _this.data.shift();
          _this.data.push(_this.data[_this.data.length - 1] + 1)
        });
      } else {
        _this.resetPosition();
      }
      
    },
    tRight() {
      console.log("right");
      this.resetPosition();
    },
    tMove(e, m) {
      this.move = "transition-duration: 0s;transform: translate3d(" + -m.x + "px," + 0 + "px,0px);";
    },
    resetPosition() {
      this.move = "transition-duration: .3s;transform: translate3d(0px,0px,0px);";
      setTimeout(() => {
        this.move = "transition-duration: 0s;transform: translate3d(0px,0px,0px);";
      }, 320);
    },
    resetPosition2(fn) {
      this.move = "transition-duration: .35s; transform: translate3d(-150%,0px,0px);";
      setTimeout(() => {
        this.move = "transition-duration: 0s;transform: translate3d(0px,0px,0px);";
        fn && fn();
      }, 320);
    }
  }
};
</script>

<style>
* {
  margin: 0;
  padding: 0;
}
body {
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
.imgWrap .it {
  position: relative;
  margin: 0 auto;
  width: 300px;
  height: 300px;
  background: red;
  z-index: 100;
  transition-property: transform, -webkit-transform;
}
.imgWrap .it:nth-child(2) {
  position: absolute;
  left: 0;
  right: 0;
  top: 0;
  bottom: 0;
  margin: 0 auto;
  z-index: 50;
  background: #d10909;
}
</style>
