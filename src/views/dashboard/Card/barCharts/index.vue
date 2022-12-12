<template>
  <div class="charts" ref="charts"></div>
</template>

<script>
import * as echarts from "echarts";
export default {
  name: "xxx",
  mounted() {
   
    // 好了 然后组件名臣自己改一下。
    // 引入方式的问题 ， 要定位的话 去package.json 里面看 echarts 的版本 发现是 5.x.x 就上去搜 echarts5 的引入方式
    let lineCharts = echarts.init(this.$refs.charts);
    console.log(lineCharts);

    lineCharts.setOption({
      xAxis: {
        show: false,
        type:"category"
      },
      yAxis: {
        show: false,
      },
      series: [
        {
          type: "bar",
          data: [10, 7, 33, 12, 48, 9,29,1,23,56,12,45],
        },
      ],

      grid: {
        left: 0,
        top: 0,
        right: 0,
        bottom: 0,
      },

      tooltip:{},
    });
  },
};
</script>

<style scoped>
.charts {
  width: 100%;
  height: 100%;
}
</style>
