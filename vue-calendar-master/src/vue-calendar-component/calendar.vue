<style scoped>
@media screen and (min-width: 460px) {
  .wh_item_date:hover {
    background: #71c7a5;
    cursor: pointer;
  }
}
* {
  margin: 0;
  padding: 0;
}

.wh_container {
  max-width: 410px;
  margin: auto;
}

li {
  list-style-type: none;
}
.wh_top_changge {
  display: flex;
}

.wh_top_changge li {
  cursor: pointer;
  display: flex;
  color: #fff;
  font-size: 18px;
  flex: 1;
  justify-content: center;
  align-items: center;
  height: 47px;
}

.wh_top_changge .wh_content_li {
  cursor: auto;
  flex: 2.5;
}
.wh_content_all {
  font-family: -apple-system, BlinkMacSystemFont, "PingFang SC",
    "Helvetica Neue", STHeiti, "Microsoft Yahei", Tahoma, Simsun, sans-serif;
  background-color: #0fc37c;
  width: 100%;
  overflow: hidden;
}

.wh_content {
  display: flex;
  flex-wrap: wrap;
  padding: 0 3% 0 3%;
  width: 100%;
}

.wh_content_body{
  position: relative;
  padding-bottom: 8px;
}

.wh_loading{
  display: flex;
  position: absolute;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  background: rgba(255, 255, 255, .5);
  font-size: 14px;
  justify-content: center;
  align-items: center;
}

.wh_content:first-child .wh_content_item_tag,
.wh_content:first-child .wh_content_item {
  color: #ddd;
  font-size: 16px;
}

.wh_content_item,
.wh_content_item_tag {
  font-size: 15px;
  width: 13.4%;
  text-align: center;
  color: #fff;
  position: relative;
}
.wh_content_item {
  height: 40px;
}

.wh_top_tag {
  width: 40px;
  height: 40px;
  line-height: 40px;
  margin: auto;
  display: flex;
  justify-content: center;
  align-items: center;
}

.wh_item_date {
  width: 40px;
  height: 40px;
  line-height: 40px;
  margin: auto;
  display: flex;
  justify-content: center;
  align-items: center;
}

.wh_jiantou1 {
  width: 8px;
  height: 8px;
  border-top: 2px solid #ffffff;
  border-left: 2px solid #ffffff;
  transform: rotate(-45deg);
}

.wh_jiantou1:active,
.wh_jiantou2:active {
  border-color: #ddd;
}

.wh_jiantou2 {
  width: 8px;
  height: 8px;
  border-top: 2px solid #ffffff;
  border-right: 2px solid #ffffff;
  transform: rotate(45deg);
}

.wh_jiantou1.disabled,
.wh_jiantou2.disabled,
.wh_jiantou1.disabled:active,
.wh_jiantou2.disabled:active{
  border-color: #fff;
  opacity: .5;
}

.wh_content_item > .wh_isMark {
  margin: auto;
  border-radius: 100px;
  background: blue;
  z-index: 2;
}
.wh_content_item .wh_other_dayhide {
  color: #bfbfbf;
}
.wh_content_item .wh_want_dayhide {
  color: #bfbfbf;
}
.wh_content_item .wh_isToday {
  background: yellow;
  border-radius: 100px;
}
.wh_content_item .wh_chose_day {
  background: green;
  border-radius: 100px;
}
</style>
<template>
  <section class="wh_container">
    <div class="wh_content_all">
      <div class="wh_top_changge">
        <li @click="PreMonth(myDate,false)">
          <div class="wh_jiantou1" :class="{disabled: !preStatus}"></div>
        </li>
        <li class="wh_content_li">{{dateTop}}</li>
        <li @click="NextMonth(myDate,false)">
          <div class="wh_jiantou2" :class="{disabled: !nextStatus}"></div>
        </li>
      </div>
      <div class="wh_content_body">
        <div class="wh_content">
          <div class="wh_content_item" v-for="tag in textTop">
            <div class="wh_top_tag">{{tag}}</div>
          </div>
        </div>
        <div class="wh_content">
          <div class="wh_content_item" v-for="(item,index) in list" @click="clickDay(item,index)">
            <div
              class="wh_item_date"
              v-bind:class="[{ wh_isMark: item.isMark},{wh_other_dayhide:item.otherMonth!=='nowMonth'},{wh_want_dayhide:item.dayHide},{wh_isToday:item.isToday},{wh_chose_day:item.chooseDay},setClass(item)]"
            >{{item.id}}</div>
            <div></div>
          </div>
          
        </div>
        <div class="wh_loading" v-if="showLoading">{{loadingText}}</div>
      </div>
      
    </div>
  </section>
</template>
<script>
import timeUtil from "./calendar";
export default {
  data() {
    return {
      myDate: [],
      list: [],
      historyChose: [],
      dateTop: "",
      preStatus: true,
      nextStatus: true,
    };
  },
  props: {
    markDate: {
      type: Array,
      default: () => []
    },
    markDateMore: {
      type: Array,
      default: () => []
    },
    textTop: {
      type: Array,
      default: () => ["一", "二", "三", "四", "五", "六", "日"]
    },
    sundayStart: {
      type: Boolean,
      default: () => false
    },
    agoDayHide: {
      type: String,
      default: `0`
    },
    futureDayHide: {
      type: String,
      default: `2554387200`
    },
    loadingText: {
      type: String,
      default: ''
    },
    showLoading: {
      type: Boolean,
      default: false,
    }
  },
  created() {
    this.myDate = new Date();
    this.intStart();
  },
  methods: {
    // 日期和最小值或最大值的比较
    compareTime(date, type='min') {
      const cDays = timeUtil.getMonthListNoOther(date);
      const cDate = type == "min" ? cDays[0].date : cDays[cDays.length-1].date;
      const ct = Math.round(new Date(cDate).getTime()/1000).toString();
      if(type == 'min') {
        if(!this.agoDayHide){
          return 1;
        }
        return ct - this.agoDayHide;
      } else {
        return this.futureDayHide - ct;
      }
    },
    compareMinOrMax() {
      this.preStatus = this.compareTime(this.myDate) <= 0 ? false : true; 
      this.nextStatus = this.compareTime(this.myDate, 'max') <= 0 ? false : true; 
    },
    intStart() {
      timeUtil.sundayStart = this.sundayStart;
      if(this.sundayStart) {
        this.textTop.unshift("日");
        this.textTop.splice(this.textTop.length-1,1)
      }
      this.compareMinOrMax();
    },
    setClass(data) {
      let obj = {};
      obj[data.markClassName] = data.markClassName;
      return obj;
    },
    clickDay: function(item, index) {
      this.myDate = new Date(item.date);
      if (item.otherMonth === "nowMonth" && !item.dayHide) {
        this.getList(this.myDate, item.date);
      }
      if (item.otherMonth !== "nowMonth") {
        if(item.otherMonth === "preMonth") {
          this.myDate = timeUtil.getOtherMonth(this.myDate, "nextMonth");   
        } else {
          this.myDate = timeUtil.getOtherMonth(this.myDate, "preMonth");   
        }
        item.otherMonth === "preMonth"
          ? this.PreMonth(item.date)
          : this.NextMonth(item.date);
      }
    },
    ChoseMonth: function(date, isChosedDay = true) {
      date = timeUtil.dateFormat(date);
      this.myDate = new Date(date);
      this.$emit("changeMonth", timeUtil.dateFormat(this.myDate));
      if (isChosedDay) {
        this.getList(this.myDate, date, isChosedDay);
      } else {
        this.getList(this.myDate);
      }
    },
    PreMonth: function(date, isChosedDay = true) {
      if(!this.preStatus) return;
      date = timeUtil.dateFormat(date);
      this.myDate = timeUtil.getOtherMonth(this.myDate, "preMonth");      
      this.$emit("changeMonth", timeUtil.dateFormat(this.myDate));
      if (isChosedDay) {
        this.getList(this.myDate, date, isChosedDay);
      } else {
        this.getList(this.myDate);
      }
      this.compareMinOrMax();
    },
    NextMonth: function(date, isChosedDay = true) {
      if(!this.nextStatus) return;
      date = timeUtil.dateFormat(date);
      this.myDate = timeUtil.getOtherMonth(this.myDate, "nextMonth");
      this.$emit("changeMonth", timeUtil.dateFormat(this.myDate));
      if (isChosedDay) {
        this.getList(this.myDate, date, isChosedDay);
      } else {
        this.getList(this.myDate);
      }
      this.compareMinOrMax();
    },
    forMatArgs: function() {
      let markDate = this.markDate;
      let markDateMore = this.markDateMore;
      markDate = markDate.map(k => {
        return timeUtil.dateFormat(k);
      });
      markDateMore = markDateMore.map(k => {
        k.date = timeUtil.dateFormat(k.date);
        return k;
      });
      return [markDate, markDateMore];
    },
    getList: function(date, chooseDay, isChosedDay = true) {
      const [markDate, markDateMore] = this.forMatArgs();
      let monthStr = date.getMonth() + 1;
      this.dateTop = `${date.getFullYear()}年${monthStr > 9 ? monthStr : ('0' + monthStr)}月`;
      let arr = timeUtil.getMonthList(this.myDate);
      let today = timeUtil.dateFormat(new Date());

      for (let i = 0; i < arr.length; i++) {
        let markClassName = "";
        let k = arr[i];
        k.chooseDay = false;
        const nowTime = k.date;
        const oday = parseInt(date.getDate());
        const day = parseInt(nowTime.split("/")[2]);
        const t = new Date(nowTime).getTime() / 1000;
        //看每一天的class
        for (const c of markDateMore) {
          if (c.date === nowTime) {
            markClassName = c.className || "";
          }
        }
        //标记选中某些天 设置class
        k.markClassName = markClassName;
        k.isMark = markDate.indexOf(nowTime) > -1;
        //无法选中某天
        k.dayHide = t < this.agoDayHide || t > this.futureDayHide;
        if (k.isToday) {
          this.$emit("isToday", nowTime);
        }
        let flag = !k.dayHide && k.otherMonth === "nowMonth";
        if (chooseDay && chooseDay === nowTime && flag) {
          this.$emit("choseDay", nowTime);
          this.historyChose.push(nowTime);
          k.chooseDay = true;
        } else if (this.historyChose[this.historyChose.length - 1] === nowTime && !chooseDay && flag ) {
          k.chooseDay = true;
        }

        if(oday == day && flag && !k.isToday) {
           k.chooseDay = true;
        }
      }
      this.list = arr;
    }
  },
  mounted() {
    this.getList(this.myDate);
  },
  watch: {
    markDate: {
      handler(val, oldVal) {
        this.getList(this.myDate);
      },
      deep: true
    },
    markDateMore: {
      handler(val, oldVal) {
        this.getList(this.myDate);
      },
      deep: true
    },
    agoDayHide: {
      handler(val, oldVal) {
        this.getList(this.myDate);
      },
      deep: true
    },
    futureDayHide: {
      handler(val, oldVal) {
        this.getList(this.myDate);
      },
      deep: true
    },
    sundayStart: {
      handler(val, oldVal) {
        this.intStart();
        this.getList(this.myDate);
      },
      deep: true
    }
  }
};
</script>