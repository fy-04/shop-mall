<template>
  <div >
    <el-card style="margin:20px 0px">
      <CategorySelect @getCategoryId="getCategoryId" :show="scene!=0"></CategorySelect>
    </el-card>
    <el-card>
      <div v-show="scene==0">
        <el-button type="primary" icon="el-icon-plus" :disabled="!category3Id" @click="addSpu">添加SPU</el-button>
        <el-table style="width:100%" border :data="records">
          <el-table-column type="index" label="序号" width="80" align="center">
          </el-table-column>
          <el-table-column prop="spuName" label="SPU名称" width="width" align="center">
          </el-table-column>
          <el-table-column prop="description" label="SPU描述" width="width" >
          </el-table-column>
          <el-table-column prop="prop" label="操作" width="width" align="center">
            <template slot-scope="{row,$index}">
              <hint-button type="success" icon="el-icon-plus" size="mini" title="添加sku" @click="addSku(row)"></hint-button>
              <hint-button type="waring" icon="el-icon-edit" size="mini" title="修改spu" @click="updateSpu(row)"></hint-button>
              <hint-button type="info" icon="el-icon-info" size="mini" title="查看当前spu全部sku列表" @click="handler(row)"></hint-button>
              <el-popconfirm title="确定删除？" @onConfirm="deleteSpu(row)">
              <hint-button type="danger" icon="el-icon-delete" size="mini" title="删除spu" ></hint-button>
              </el-popconfirm>
            </template>
          </el-table-column>
        </el-table>
        <el-pagination
        style="text-align:center"
        :current-page="page"
        :page-sizes="[3,5,10]"
        :page-size="limit"
        layout="prev,pager,next,jumper,->,sizes,total"
        @current-change="getSpuList"
        @size-change="handleSizeChange"
        :total="total"
        ></el-pagination>
      </div>
       <SpuForm v-show="scene==1" @changeScene="changeScene" ref="spu">添加SPU</SpuForm>
       <SkuForm v-show="scene==2" ref="sku" @changeScene="changeScene">添加SKu</SkuForm>
      </el-card>

      <el-dialog :title="`${spu.spuName}的sku列表收货地址`" :visible.sync="dialogTableVisible" :before-close="close">
        <el-table :data="skuList" style="width:100%" border v-loading="loading">
          <el-table-column prop="skuName" label="名称" width="width">
          </el-table-column>
          <el-table-column prop="price" label="价格" width="width">
          </el-table-column>
          <el-table-column prop="weight" label="重量" width="width">
          </el-table-column>
          <el-table-column prop="默认图片" label="" width="width">
            <template slot-scope="{row,$index}">
              <img :src="row.skuDefaultImg" alt="" style="width:100px;height:100px">
            </template>
          </el-table-column>
        </el-table>
      </el-dialog>
  </div>
 
  
</template>

<script>
import SpuForm from './SpuForm';
import SkuForm from './SkuForm';
export default {
    name:'Spu',
    data(){
      return {
        category1Id:'',
        category2Id:'',
        category3Id:'',
        show:true,
        page:1,
        limit:3,
        records:[],
        total:0,
        scene:0,
        dialogTableVisible:false,
        spu:{},
        skuList:[],
        loading:true,
      };
    },
    methods:{
      getCategoryId({categoryId,level}){
        if(level==1){
          this.category1Id=categoryId;
          this.category2Id='';
          this.category3Id='';
        }else if(level==2){
          this.category2Id=categoryId;
          this.category3Id='';
        }else{
          this.category3Id=categoryId;
          this.getSpuList();
        }
      },
      async getSpuList(pages=1){
        this.page=pages;
        const {page,limit,category3Id}=this;
        let result=await this.$API.spu.reqSpuList(page,limit,category3Id);
        console.log(result);
        if(result.code==200){
          this.total=result.data.total;
          this.records=result.data.records;
        }
      },
      handleSizeChange(limit){
        this.limit=limit;
        this.getSpuList();
      },
      addSpu(){
        this.scene=1;
        // 调用子组件中的方法
        this.$refs.spu.addSpuData(this.category3Id);
      },
      // 修改spu模块的触发
      updateSpu(row){
        this.scene=1;
        this.$refs.spu.initSpuData(row);
        console.log('*******',this.$refs.spu)
      },
      // 自定义事件
      changeScene(scene,flag){
        this.scene=scene;
        if(flag=='修改'){
          this.getSpuList(this.page);
        }else{
          this.getSpuList();
        }
      },
      async deleteSpu(row){
        let result=await this.$API.spu.reqDeleteSpu(row.id);
        if(result.code==200){
          this.$message({type:"success",message:"删除成功"});
          this.getSpuList(this.records.length>1?this.page:this.page-1);
        }
      },
      addSku(row){
        
        this.scene=2;
        // 调用子组件sku中的方法
        this.$refs.sku.getData(this.category1Id,this.category2Id,row);
      },
      changeScene(scene){
        this.scene=scene;

      },
      async handler(spu){
        this.dialogTableVisible=true;
        this.spu=spu;
        let result=await this.$API.spu.reqSkuList(spu.id);
        if (result.code==200){
          this.skuList=result.data;
          this.loading=false;
        }
        
        
      },
      close(){
        this.loading=true;
        this.skuList=[];
      }
    },
    components:{
      SpuForm,
      SkuForm
    }

}
</script>

<style scoped>

</style>