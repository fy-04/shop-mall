<template>
  <div>
    <el-select placeholder="请选择" v-model="category1Id" @change="getData">
      <el-option
        :label="c1.tmName"
        :value="c1.id"
        v-for="(c1, index) in list"
        :key="c1.id"
      ></el-option>
    </el-select>
    <el-button @click="flag = !flag">改变状态</el-button>

    <el-table v-if="flag" :data="list" stripe style="width: 100%">
      <el-table-column prop="id" label="序号" width="180"> </el-table-column>
      <el-table-column prop="tmName" label="品牌名称" width="180">
      </el-table-column>

      <el-table-column prop="logoUrl" label="图片">
        <template slot-scope="{ row, $index }">
          <img :src="row.logoUrl" alt="" style="width: 100px; height: 100px" />
        </template>
      </el-table-column>

      <el-table-column>
        <template slot-scope="{ row, $index }">
          <el-button
            type="primary"
            icon="el-icon-edit"
            @click="toEdit(row)"
          ></el-button>
          <el-button
            type="primary"
            icon="el-icon-delete"
            @click="toDelete(row)"
          ></el-button>
        </template>
      </el-table-column>
    </el-table>

    <el-pagination
      style="margin-top: 20px; textaalign: center"
      :current-page="page"
      :page-size="limit"
      :pager-count="10"
      :total="total"
      @current-change="getData"
      layout="prev,pager,next,jumper,->,sizes,total"
    >
    </el-pagination>
    <!-- 在current-page事件的回调函数中，回调的参数是当前页面
         于是可以在拉取数据的函数中传入此参数
     -->

    <!-- :page-sizes="[3, 5, 10]"
      :page-size="limit"
       @size-change="handleSizeChange" -->
  </div>
</template>

<script>
export default {
  name: "test233",
  data() {
    return {
      flag:true,
      category1Id: "",
      list: [],
      page: 1,
      limit: 10,
      total: 0,
      arr1:[2,5,6,8,9,1,67,54],
    };
  },
  mounted() {
    this.getData();
    let result=this.sort(this.arr1);
    
    let result2=this.sum(this.arr1);
    let result3=this.sum1(this.arr1);
    console.log(result,'****',result2,'++++',result3);
  },

  methods: {
    async getData(currentpage = 1) {
      this.page = currentpage;

      let result = await this.$API.trademark.reqTradeMarkList(
        this.page,
        this.limit
      );
      console.log(result);
      if (result.code == 200) {
        this.list = result.data.records;
        this.total = result.data.total;
      }
    },
    toEdit(row) {},
    toDelete(row) {
      this.$confirm("此操作将永久删除该文件, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning",
      })
        .then(() => {
          let result = this.$API.trademark.reqDeleteTradeMark(row.id);
          if (result.code == 200) {
            this.$message({
              type: "success",
              message: "删除成功!",
            });
          }
          this.getData(this.list.length > 1 ? this.page : this.page - 1);
        })
        .catch(() => {
          this.$message({
            type: "info",
            message: "已取消删除",
          });
        });
    },

    // 冒泡函数
    sort(arr){
      var temp;
      for(var i=0;i<arr.length;i++){
        for( var j=0;j<arr.length-i;j++){
          if(arr[j+1]<arr[j]){
            temp=arr[j+1];
            arr[j+1]=arr[j];
            arr[j]=temp;
          }

        }
      }
      return arr;
    },
    // 加和1
    sum(arr){
      let he=0;
      for(var i=0;i<arr.length;i++){
         he+=arr[i];      
      }
      return he;
    },
    // reduce 为数组中的每一个元素依次执行回调函数callback，callback接收四个参数值
    sum1(arr){
      return arr.reduce((prev,cur)=>{
        return prev+cur;
      });
    }
  },
};
</script>

<style lang="scss" scoped>
</style>