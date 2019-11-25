<template>
  <div id="calendar" v-clickoutside="handleClose" v-cloak>
    <div class="lq_box">
      <input
        type="text"
        :class="{ Border: isBorder }"
        readonly
        @click="isBorder = !isBorder"
      />
      <i class="iconfont  icon-rili lq_rl"></i>
    </div>
    <transition name="slide-fade">
      <div class="dropdown" v-show="isBorder">
        <div class="headle">
          <div class="headle_one">
            <i class="iconfont icon-shuangjiantouzuo" @click="minusYear"></i>
            <i
              class="iconfont icon-zuojiantou lq_Month"
              @click="minusMonth"
            ></i>
          </div>
          <div class="headle_two">
            <span class="span_one">{{ LastYear.split("/")[0] }}年</span>
            <span class="span_two">{{ LastYear.split("/")[1] }}月</span>
          </div>
          <div class="headle_one">
            <i
              class="iconfont icon-changyongtubiao-xianxingdaochu-zhuanqu-"
              @click="addMonth"
            ></i>
            <i
              class="iconfont icon-changyongicon- lq_Month"
              @click="addYear"
            ></i>
          </div>
        </div>
        <div class="center_box">
          <span>日</span>
          <span>一</span>
          <span>二</span>
          <span>三</span>
          <span>四</span>
          <span>五</span>
          <span>六</span>
        </div>
        <div class="foot_box">
          <div class="foot_div">
            <span
              class="foot_span"
              v-for="item in oneMonth"
              :key="item.date"
              :style="{ color: item.color }"
              >{{ item.date.split("/")[2]
              }}<i v-if="item.isOk" class="foot_i"></i
            ></span>
          </div>
        </div>
        <div class="foot_t">
          <i class="foot_i"></i>
          <span>提交的日志</span>
        </div>
      </div>
    </transition>
  </div>
</template>
<script>
export default {
  name: "ym",
  props: {
    YM: {
      type: [Array],
      default: false
    }
  },
  data() {
    return {
      isBorder: true,
      oneMonth: [], // 一个月的全部日期
      LastSeven: [], // 上个月的最后七天
      NextMonth: [], // 下个月的前七天
      LastYear: this.dateFormat(new Date())
    };
  },
  watch: {
    LastYear() {
      this.getOneMonth(this.LastYear);
      this.getLastMonthSeven(); // 上个月七天
      this.getNextMonthSeven(); // 下个月七天
      this.getOneday(); // 补全上月
      this.getlastDay(); // 补全下月
      if (this.YM.length) {
        this.labelImportant();
      }
    }
  },
  // 自定义指令实现点击空白处隐藏下拉列表
  directives: {
    clickoutside: {
      bind: function(el, binding, vnode) {
        // console.log(el, binding, vnode);
        function documentHandler(e) {
          // console.log(e);
          if (el.contains(e.target)) {
            return false;
          }
          if (binding.expression) {
            binding.value(e);
          }
        }
        el._vueClickOutside_ = documentHandler;
        document.addEventListener("click", documentHandler);
      },
      unbind: function(el, binding) {
        // console.log(el);
        document.removeEventListener("click", el._vueClickOutside_);
        delete el._vueClickOutside_;
      }
    }
  },
  async created() {
    await this.getOneMonth(new Date()); // 获取一个月的全部日期
    this.getLastMonthSeven(); // 上个月七天
    this.getNextMonthSeven(); // 下个月七天
    this.getOneday(); // 补全上月
    this.getlastDay(); // 补全下月
    console.log(this.YM.length);
    if (this.YM.length) {
      this.labelImportant();
    }
    // this.Year = this.dateFormat(new Date())
  },
  methods: {
    // 转化日期格式
    dateFormat(now) {
      var year = now.getFullYear(); //取得4位数的年份
      var month =
        now.getMonth() + 1 < 10
          ? "0" + (now.getMonth() + 1)
          : now.getMonth() + 1; //取得日期中的月份，其中0表示1月，11表示12月
      var date = now.getDate() < 10 ? "0" + now.getDate() : now.getDate(); //返回日期月份中的天数（1到31）
      return year + "/" + month + "/" + date;
    },
    // 下拉菜单
    handleClose() {
      this.isBorder = false;
    },
    minusYear() {
      // console.log(this.LastYear.split('/')[1]-1);
      this.LastYear =
        this.LastYear.split("/")[0] - 1 + "/" + this.LastYear.split("/")[1];
      // console.log(this.LastYear)
      // this.getOneMonth(this.LastYear)
      // this.getLastMonthSeven(); // 上个月七天
      // this.getNextMonthSeven(); // 下个月七天
      // this.getOneday(); // 补全上月
      // this.getlastDay(); // 补全下月
    },
    minusMonth() {
      // console.log("minusMonth");
      // console.log(this.LastYear.split('/')[0]+'/'+(this.LastYear.split('/')[1]-1))
      if (this.LastYear.split("/")[1] - 1 <= 0) {
        this.LastYear = this.LastYear.split("/")[0] - 1 + "/" + "12";
        // console.log(this.LastYear)
        return;
      }
      this.LastYear =
        this.LastYear.split("/")[0] + "/" + (this.LastYear.split("/")[1] - 1);
    },
    addMonth() {
      console.log(this.LastYear.split("/")[1] * 1 + 1);
      if (this.LastYear.split("/")[1] * 1 + 1 > 12) {
        this.LastYear = this.LastYear.split("/")[0] * 1 + 1 + "/" + "1";
        console.log(this.LastYear);
        return;
      }
      this.LastYear =
        this.LastYear.split("/")[0] +
        "/" +
        (this.LastYear.split("/")[1] * 1 + 1);
    },
    addYear() {
      // console.log('addYear')
      this.LastYear =
        this.LastYear.split("/")[0] * 1 + 1 + "/" + this.LastYear.split("/")[1];
    },
    // 获取当月的天数
    getCountDays(time) {
      var curDate = new Date(time);
      console.log(curDate);
      /* 获取当前月份 */
      var curMonth = curDate.getMonth();
      /*  生成实际的月份: 由于curMonth会比实际月份小1, 故需加1 */
      curDate.setMonth(curMonth + 1);
      /* 将日期设置为0, 这里为什么要这样设置, 我不知道原因, 这是从网上学来的 */
      curDate.setDate(0);
      /* 返回当月的天数 */
      return curDate.getDate();
    },
    // 获取一个月的全部日期
    getOneMonth(times) {
      return new Promise((resolve, reject) => {
        // 一个月有多少天
        const numberDay = this.getCountDays(times);
        // console.log(numberDay)
        // 获取当前的年/月,用于拼接
        let time =
          new Date(times).getFullYear() +
          "/" +
          (new Date(times).getMonth() + 1 < 10
            ? "0" + (new Date(times).getMonth() + 1)
            : new Date(times).getMonth() + 1);
        // console.log(time)
        // 获取一个月的所有日期
        let arrTime = [];
        // 找到当前日期设置不同的颜色
        for (var i = 1; i <= numberDay; i++) {
          // console.log(i);
          // console.log((this.dateFormat(new Date())) == time + "/" + i)
          if (this.dateFormat(new Date()) == time + "/" + i) {
            arrTime.push({ date: time + "/" + i, color: "#40b8ff" });
            continue;
          }
          arrTime.push({ date: time + "/" + i, color: "#606266" });
        }
        this.oneMonth = arrTime;
        resolve(arrTime);
        console.log(arrTime);
      });
    },
    // 获取当前日期是星期几
    getWhatDay(times) {
      return new Date(times).getDay();
    },
    // 获取上个月的最后一天
    getLastMonth(times) {
      var nowdays = new Date(times);
      var year = nowdays.getFullYear();
      var month = nowdays.getMonth();
      if (month == 0) {
        month = 12;
        year = year - 1;
      }
      if (month < 10) {
        month = "0" + month;
      }
      var firstDay = year + "-" + month + "-" + "01"; //上个月的第一天
      var myDate = new Date(year, month, 0);
      var lastDay = year + "-" + month + "-" + myDate.getDate(); //上个月的最后一天
      return {
        ym: year + "/" + month, // 上个月的年/月
        lastDay
      };
    },
    // 获取上个月的最后七天
    getLastMonthSeven() {
      let week = this.getLastMonth(this.oneMonth[0].date);
      // console.log(week, week.lastDay.split("-")[2] * 1);
      let arr = [];
      for (var i = 7; i >= 0; i--) {
        arr.push({
          date: week.ym + "/" + (week.lastDay.split("-")[2] * 1 - i),
          color: "#ccc4cc"
        });
      }
      this.LastSeven = arr;
      // console.log(arr);
      // console.log(arr.slice(-5));
    },
    // 获取下个月的时间
    getNextMonth(date) {
      var arr = date.split("/");
      var year = arr[0]; //获取当前日期的年份
      var month = arr[1]; //获取当前日期的月份
      var day = arr[2]; //获取当前日期的日
      var days = new Date(year, month, 0);
      days = days.getDate(); //获取当前日期中的月的天数
      var year2 = year;
      var month2 = parseInt(month) + 1;
      if (month2 == 13) {
        year2 = parseInt(year2) + 1;
        month2 = 1;
      }
      var day2 = day;
      var days2 = new Date(year2, month2, 0);
      days2 = days2.getDate();
      if (day2 > days2) {
        day2 = days2;
      }
      if (month2 < 10) {
        month2 = "0" + month2;
      }
      var Nextday = year2 + "-" + month2 + "-" + day2;
      return {
        ym: year2 + "/" + month2,
        Nextday
      };
    },
    // 获取下个月的前七天
    getNextMonthSeven() {
      let week = this.getNextMonth(this.oneMonth[0].date);
      // console.log(week)
      let arr = [];
      for (var i = 0; i < 7; i++) {
        arr.push({
          date: `${week.ym}/${week.Nextday.split("-")[2] * 1 + i}`,
          color: "#ccc4cc"
        });
      }
      // console.log(arr)
      this.NextMonth = arr;
    },
    // 补全上个月时间
    getOneday() {
      let day = this.getWhatDay(this.oneMonth[0].date);
      console.log(day);
      if (day == 0) {
        return;
      }
      let arr = this.LastSeven.slice(-day);
      // console.log(arr)
      this.oneMonth = arr.concat(this.oneMonth);
      // console.log(this.oneMonth)
    },
    // 补全下个月时间
    getlastDay() {
      let day = this.getWhatDay(this.oneMonth[this.oneMonth.length - 1].date);
      console.log(day);
      if (6 - day != 0) {
        let arr = this.NextMonth.splice(6 - day);
        console.log(arr, this.NextMonth);
        this.oneMonth = this.oneMonth.concat(this.NextMonth);
      }
    },
    // 标注重要日子
    labelImportant() {
      console.log(this.oneMonth);
      let index;
      this.YM.forEach(item => {
        index = this.oneMonth.findIndex(items => items.date == item);
        if (index != -1) {
          console.log(index);
          this.oneMonth[index].isOk = true;
        }
      });
    }
  }
};
</script>
<style lang="scss" scoped>
// 作用是取消数据绑定时出现的代码闪烁
[v-cloak] {
  display: none;
}
#calendar {
  position: relative;
  .lq_box {
    width: 220px;
    position: relative;
    input {
      width: 220px;
      height: 42px;
      background-color: #ffffff;
      border-radius: 5px;
      border: solid 1px #e4e4e4;
      outline: none;
      cursor: pointer;
    }
    .Border {
      border: solid 1px #409eff;
    }
    .lq_rl {
      position: absolute;
      top: 50%;
      right: 14px;
      transform: translateY(-50%);
      color: #999999;
    }
  }
  .dropdown {
    height: 390px;
    width: 329px;
    border-radius: 5px;
    background-color: #ffffff;
    box-shadow: 1px 1px 10px 0px #409eff;
    position: absolute;
    right: 0px;
    margin-top: 6px;
    // animation: downName 5s;
    padding: 25px 25px 0 25px;
    box-sizing: border-box;
    .headle {
      display: flex;
      justify-content: space-between;
      margin-bottom: 40px;
      .headle_one {
        i {
          cursor: pointer;
          color: #999;
        }
        .lq_Month {
          margin-left: 18px;
        }
      }
      .headle_two {
        color: #606266;
        span {
          cursor: pointer;
        }
        span:nth-child(1) {
          margin-right: 11px;
        }
        .span_one:hover,
        .span_two:hover {
          color: #409eff;
        }
      }
    }
    .center_box {
      height: 25px;
      font-size: 10px;
      margin-bottom: 14px;
      display: flex;
      justify-content: space-around;
      color: #606266;
      border-bottom: 1px solid #ebeef5;
    }
    .foot_box {
      .foot_div {
        // height: 38px;
        display: flex;
        justify-content: space-around;
        flex-flow: column wrap-reverse;
        width: 278px;
        flex-wrap: wrap;
        flex-direction: row;
        font-size: 12px;
        span {
          display: inline-block;
          width: 39px;
          height: 38px;
          text-align: center;
          position: relative;
          flex-shrink: 0;
          cursor: pointer;
          .foot_i {
            display: inline-block;
            width: 3px;
            height: 3px;
            border-radius: 50%;
            background: #0048ff;
            position: absolute;
            bottom: 50%;
            right: 50%;
            transform: translate(50%, 50%);
          }
        }
      }
    }
    .foot_t {
      position: relative;
      line-height: 20px;
      .foot_i {
        display: inline-block;
        width: 3px;
        height: 3px;
        border-radius: 50%;
        background: #0048ff;
        position: absolute;
        bottom: 50%;
        left: 0;
        transform: translate(50%, 50%);
      }
      span {
        margin-left: 11px;
        color: #adadad;
        font-size: 14px;
      }
    }
  }
}
@keyframes downName {
  0% {
    display: none;
  }
  100% {
    display: block;
  }
}
.slide-fade-enter-active,
.slide-fade-leave-active {
  transition: all 0.5s;
}
.slide-fade-leave-to,
.slide-fade-enter 
/* .slide-fade-leave-active 用于 2.1.8 以下版本 */ {
  transform: translateY(-10px);
  opacity: 0;
}
</style>
