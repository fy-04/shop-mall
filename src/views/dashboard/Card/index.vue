<template>
    <div>
        <el-row :gutter="10">
            <el-col :span="6">
                <el-card>
                    <Detail title="总销售额" count="88995">
                        <template slot="charts">
                            <span>周同比&nbsp;&nbsp;56.67%</span>
                            &nbsp;&nbsp;
                            <span>日同比&nbsp;&nbsp;19.96%&nbsp;</span>
                        </template>
                        <template slot="footer">
                            <span>日销售额度 12345</span>
                        </template>
                    </Detail>
                </el-card>            
            </el-col>
            <el-col :span="6">
                <el-card>
                    <Detail title="访问量" count="122995">
                        <template slot="charts">
                            <lineCharts></lineCharts>
                        </template>
                        <template slot="footer">
                            <span>日销售额度 12345</span>
                        </template>
                    </Detail>
                </el-card>    
            </el-col>
            <el-col :span="6">
                <el-card>
                    <Detail title="支付笔数" count="88995">
                        <template slot="charts">
                            <barCharts></barCharts>
                        </template>
                        <template slot="footer">
                            <span>日销售额度 12345</span>
                        </template>
                    </Detail>
                </el-card>    
            </el-col>
            <el-col :span="6">
                <el-card>
                    <Detail title="运营活动效果" count="88995">
                        <template slot="charts">
                            <progressCharts></progressCharts>
                        </template>
                        <template slot="footer">
                            <span>日销售额度 12345</span>
                        </template>
                    </Detail>
                </el-card>    
            </el-col>
        </el-row>
        
    </div>
    
</template>

<script>
import Detail from './Detail'
import lineCharts from './lineChart'
import barCharts from './barCharts'
import progressCharts from './progressCharts'

export default {
   name:'Card',
   components:{
    Detail,
    lineCharts,
    barCharts,
    progressCharts,
   }
}
</script>

<style scoped>


</style>
