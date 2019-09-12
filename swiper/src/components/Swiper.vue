<template>
  <div class="block-swiper">
    <div class="swiper-container">
      <div class="swiper-wrapper">
        <div class="swiper-slide" v-for="(item, index) in datas" :key="index">
          <div v-if="item">{{item}}</div>
          <div v-if="item === null">
            <span>接口请求错误</span>
            <button @click="rGetData">刷新</button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import "swiper/dist/css/swiper.css";
import Swiper from "swiper";

export default {
  name: "Swiper",
  props: ["swiperOption", "datas"],
  data() {
    return {
      $swiper: null
    };
  },
  watch: {
    datas: {
      handler() {
        this.swiperUpdate();
      }
    }
  },
  mounted() {
    console.log(this.$listeners)
    this.$swiper = new Swiper(".swiper-container", this.swiperOption);
    this.$on("hook:destroyed", () => {
      this.$swiper.destroy(true, true);
    });
  },
  methods: {
    swiperUpdate() {
      this.$nextTick(() => {
        this.$swiper.updateSlides();
        this.$swiper.updateSlidesClasses()
      });
    },
    rGetData() {
      this.$listeners.getData();
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.block-swiper {
  margin: 0 auto;
  width: 100%;
  height: 100%;
  max-width: 540px;
  
}
.swiper-container {
  height: 100%;
}
.swiper-slide{
  width: 345px!important;
  margin: 0 15px;
  background: #fff;
}

.swiper-slide-prev {
  transform: translateX(23px)
}
.swiper-slide-next {
  transform: translateX(-23px)
}
</style>
