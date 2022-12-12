<template>
  <el-card class="box-card" style="margin: 10px 0px">
    <div slot="header" class="clearfix">
      <!-- v-model="activeName" @tab-click="handleClick" -->
      <el-tabs class="tab" v-model="activeName">
        <el-tab-pane label="用户管理" name="销售额">用户管理</el-tab-pane>
        <el-tab-pane label="配置管理" name="访问量">配置管理</el-tab-pane>
      </el-tabs>
      <div class="right">
        <span>今日</span>
        <span>本周</span>
        <span>本月</span>
        <span>本年</span>

        <el-date-picker
          class="date"
          type="daterange"
          range-separator="-"
          start-placeholder="开始日期"
          end-placeholder="结束日期"
          size="mini"
        >
        </el-date-picker>
      </div>
    </div>

    <div>
      <el-row :gutter="10">
        <el-col :span="18">
          <div class="charts" ref="charts"></div>
        </el-col>
        <el-col :span="6">
          <div>
            <ul>
              <li>
                <span class="rindex">1</span>
                <span>肯德基</span>
                <span class="rvalue">356789</span>
              </li>
              <li>
                <span class="rindex">1</span>
                <span>肯德基</span>
                <span class="rvalue">356789</span>
              </li>
              <li>
                <span class="rindex">1</span>
                <span>肯德基</span>
                <span class="rvalue">356789</span>
              </li>
              <li>
                <span class="rindex">1</span>
                <span>肯德基</span>
                <span class="rvalue">356789</span>
              </li>
              <li>
                <span class="rindex">1</span>
                <span>肯德基</span>
                <span class="rvalue">356789</span>
              </li>
              <li>
                <span class="rindex">1</span>
                <span>肯德基</span>
                <span class="rvalue">356789</span>
              </li>
              <li>
                <span class="rindex">1</span>
                <span>肯德基</span>
                <span class="rvalue">356789</span>
              </li>
            </ul>
          </div>
        </el-col>
      </el-row>
    </div>
  </el-card>
</template>

<script>
import * as echarts from "echarts";
export default {
  name: "Sale",
  components: {},
  data(){
    return{
      activeName:"销售额"
    }
  },
  mounted(){
    let mycharts=echarts.init(this.$refs.charts);
    mycharts.setOption({
       title:{
        text:"销售额趋势"
      },
        tooltip: {
    trigger: 'axis',
    axisPointer: {
      type: 'shadow'
    }
  },
  grid: {
    left: '3%',
    right: '4%',
    bottom: '3%',
    containLabel: true
  },
  xAxis: [
    {
     
      type: 'category',
      data: ['一月', '二月', '三月', '四月', '五月', '六月', '七月', '五月', '六月', '七月', '五月', '六月'],
      axisTick: {
        alignWithLabel: true
      }
    }
  ],
  yAxis: [
    {
      type: 'value'
    }
  ],
  series: [
    {
      name: 'Direct',
      type: 'bar',
      barWidth: '60%',
      data: [10, 52, 200, 334, 390, 330, 220,10, 52, 200, 334, 390]
    }]
    })

  }
};
</script>

<style scoped>
.clearfix {
  position: relative;
  display: flex;
  justify-content: space-between;
}
span{
  padding: 0px 20px;
}
.tab {
  width: 100%;
}
.right {
  position: absolute;
  right: 0px;
}
.date {
  width: 200px;
  margin: 0px 20px;
}
.right span {
  margin: 0px 10px;
}
.charts{
  width:100%;
  height: 300px;
}
ul{
  list-style: none;
  width: 100%;
  height: 300px;
}
ul li{
  margin: 10px 0px;
  height: 8%;
  width: 100%;
  float: right;
}
.rindex{
  float: left;
  width:25px;
  height: 25px;
  border-radius: 50%;
  background-color: black;
  color: white;
  text-align: center;
}
.rvalue{
  float: right;
}
</style>
