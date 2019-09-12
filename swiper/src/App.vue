<template>
  <div id="app">
    <Swiper :datas="datas" :swiperOption="swiperOption" @getData="getData" v-if="datas.length > 0"></Swiper>
    <div v-if="datas.length == 0" class="emptyPage">数据初始化中...</div>
  </div>
</template>

<script>
import Swiper from "./components/Swiper.vue";
let vm = null;
export default {
  name: "app",
  components: {
    Swiper
  },
  data() {
    return {
      pageCount: 0,
      page: 1,
      datas: [],
      slideLock: false,
      swiperOption: {
        loop: false,
        // slidesPerView: 1,
        // spaceBetween: 8,
        on: {
          slideNextTransitionEnd: function() {
            let index = this.activeIndex + 1;
            if (vm.isLastSlide(index) && vm.isAddSlide(index)) {
              vm.addSlide(index);
            }
          }
        }
      },
      ss: true,
    };
  },
  created() {
    vm = this;
    this.addSlide(this.page);
  },
  methods: {
    isLastSlide(index) {
      return index == this.datas.length;
    },
    isAddSlide(page) {
      return page <= this.pageCount && !this.slideLock;
    },
    addEmptySlide(status) {
      this.datas.push("");
    },
    addSlide(index) {
      this.slideLock = true;
      this.page = index;
      this.getData();
    },
    getData() {
      setTimeout(() => {
        let data = this.page;
        if(this.page == 2 && this.ss) {
          data = null;
          this.ss = false;
        }
        !data ? this.slideLock = true : this.slideLock = false;
        this.$set(this.datas, this.page - 1, data);
        if (!this.pageCount) this.pageCount = 4;
        if (this.isAddSlide(this.page + 1)) {
          this.addEmptySlide();
        } else {
          this.slideLock = true;
        }
      }, 1000);
    }
  }
};
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
html,
body {
  height: 100%;
}
#app {
  height: 100%;
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  background: #f5f5f5;
}
.emptyPage {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100%;
  background: #fff;
}
</style>
