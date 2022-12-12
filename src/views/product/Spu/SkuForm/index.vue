<template>
  <div>
    <el-form ref="form" label-width="80px">
      <el-form-item label="SPU名称">{{ spuName }}</el-form-item>
      <el-form-item label="SKU名称">
        <el-input placeholder="sku名称" v-model="skuInfo.skuName"></el-input>
      </el-form-item>
      <el-form-item label="价格（元）">
        <el-input
          placeholder="价格（元素）"
          type="number"
          v-model="skuInfo.price"
        ></el-input>
      </el-form-item>
      <el-form-item label="重量（千克）">
        <el-input
          placeholder="重量（千克）"
          v-model="skuInfo.weight"
        ></el-input>
      </el-form-item>
      <el-form-item label="规格描述">
        <el-input type="textarea" rows="4" v-model="skuInfo.skuDesc"></el-input>
      </el-form-item>

      <el-form-item label="平台属性">
        <el-form :inline="true" ref="form" label-width="80px">
          <el-form-item
            :label="attr.attrName"
            v-for="(attr, index) in attrInfoList"
            :key="attr.id"
          >
            <el-select placeholder="请选择" v-model="attr.attrIdAndValueId">
              <el-option
                :label="attrValue.valueName"
                :value="`${attr.id}:${attrValue.id}`"
                v-for="(attrValue, index) in attr.attrValueList"
                :key="attrValue.id"
              ></el-option>
            </el-select>
          </el-form-item>
        </el-form>
      </el-form-item>
      <el-form-item label="销售属性">
        <el-form :inline="true" ref="form" label-width="80px">
          <el-form-item
            :label="saleAttr.saleAttrName"
            v-for="(saleAttr, index) in spuSaleAttrList"
            :key="saleAttr.id"
          >
            <el-select placeholder="请选择" v-model="saleAttr.attrIdAndValueId">
              <el-option
                :label="saleAttrValue.saleAttrValueName"
                :value="`${saleAttr.id}:${saleAttrValue.id}`"
                v-for="(saleAttrValue, index) in saleAttr.spuSaleAttrValueList"
                :key="saleAttrValue.id"
              ></el-option>
            </el-select>
          </el-form-item>
        </el-form>
      </el-form-item>

      <el-form-item label="图片列表">
        <el-table
          style="width: 100%"
          border
          :data="spuImageList"
          @selection-change="handleSelectionChange"
        >
          <el-table-column
            type="selection"
            prop="prop"
            label="label"
            width="80"
          ></el-table-column>
          <el-table-column prop="prop" label="图片" width="width">
            <template slot-scope="{ row, $index }">
              <img :src="row.imgUrl" style="width: 100px; height: 100px" />
            </template>
          </el-table-column>
          <el-table-column
            prop="imgName"
            label="名称"
            width="width"
          ></el-table-column>
          <el-table-column prop="prop" label="操作" width="width">
            <template slot-scope="{ row, index }">
              <el-button
                type="primary"
                v-if="row.isDefault == 0"
                @click="changeDefault(row)"
                >设置默认</el-button
              >
              <el-button v-else>默认</el-button>
            </template>
          </el-table-column>
        </el-table>
      </el-form-item>

      <el-form-item>
        <!-- @click="save" -->
        <el-button type="保存" @click="save">保存</el-button>
        <el-button @click="cancel">取消</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>

<script>
export default {
  name: "",
  data() {
    return {
      spuImageList: [],
      spuSaleAttrList: [],
      attrInfoList: [],
      spuName: "",
      imageList: [],
      skuInfo: {
        category3Id: 0,
        spuId: 0,
        tmId: 0,

        skuName: "",
        price: 0,
        weight: "",
        skuDesc: "",

        skuImageList: [],
        skuAttrValueList: [],
        skuSaleAttrValueList: [
          {
            // id:0,
            // saleAttrId:0,
            // saleAttrName:"string",
            // saleAttrValueId:0,
            // saleAttrValueName:"string",
            // skuId:0,
            // spuId:0,
          },
        ],
        skuDefaultImg: [],
      },
    };
  },
  methods: {
    // 从父组件调用此方法
    async getData(category1Id, category2Id, spu) {
      this.skuInfo.category3Id = spu.category3Id;
      this.skuInfo.spuId = spu.id;
      this.skuInfo.tmId = spu.tmId;
      this.spuName = spu.spuName;

      let result = await this.$API.sku.reqSpuImageList(spu.id);
      if (result.code == 200) {
        let list = result.data;
        list.forEach((item) => {
          item.isDefault = 0;
        });
        this.spuImageList = list;
      }

      let result1 = await this.$API.sku.reqSpuSaleAttrList(spu.id);
      if (result1.code == 200) {
        this.spuSaleAttrList = result1.data;
        console.log(this.spuSaleAttrList, 2333);
      }

      let result2 = await this.$API.sku.reqAttrInfoList(
        category1Id,
        category2Id,
        spu.category3Id
      );
      if (result2.code == 200) {
        this.attrInfoList = result2.data;
      }
      console.log(result1);
    },
    handleSelectionChange(params) {
      this.imageList = params;
    },
    changeDefault(row) {
      this.spuImageList.forEach((item) => {
        item.isDefault = 0;
      });
      row.isDefault = 1;
      this.skuInfo.skuDefaultImg = row.imgUrl;
    },
    cancel() {
      this.$emit("changeScene", 0);
      Object.assign(this._data, this.$options.data());
    },
    // async save() {
    //   const { attrInfoList, skuInfo, imageList,spuSaleAttrList } = this;

    //   skuInfo.skuAttrValueList = attrInfoList.reduce((prev, item) => {
    //     if (item.attrIdAndValueId) {
    //       const [attrId, valueId] = item.attrIdAndValueId.split(":");
    //       prev.push({ attrId, valueId });
    //     }
    //     return prev;
    //   }, []);

    //   skuInfo.skuSaleAttrValueList=spuSaleAttrList.reduce((prev,item)=>{
    //     if(item.attrIdAndValueId){
    //       const[saleAttrId,saleAttrValueId]=item.attrIdAndValueId.split(':');
    //       prev.push({saleAttrId,saleAttrValueId});
    //     }
    //     return prev;
    //   },[]);

    //   skuInfo.skuImageList = imageList.map((item) => {
    //     return {
    //       imgName: item.imgName,
    //       imgUrl: item.imgUrl,
    //       isDefault: item.isDefault,
    //       spuImgId: item.id,
    //     };
    //   });

    //   // debugger;
    //   console.log(this.$API.sku,111111111111111111);

    //   let result = await this.$API.sku.reqAddSku(skuInfo);
    //   console.log(result);
    // },
   async save(){
      //整理参数
      //整理平台属性
      const {attrInfoList,skuInfo,spuSaleAttrList,imageList} = this;
      //整理平台属的数据
     skuInfo.skuAttrValueList = attrInfoList.reduce((prev,item)=>{
         if(item.attrIdAndValueId){
           const [attrId,valueId]  = item.attrIdAndValueId.split(":");
            prev.push({attrId,valueId});
         }
         return prev;
      },[]);
      //整理销售属性
     skuInfo.skuSaleAttrValueList = spuSaleAttrList.reduce((prev,item)=>{
         if(item.attrIdAndValueId){
           const [saleAttrId,saleAttrValueId] = item.attrIdAndValueId.split(':');
           prev.push({saleAttrId,saleAttrValueId});
         }
         return prev;
      },[]);
     //整理图片的数据
     skuInfo.skuImageList = imageList.map(item=>{
         return {
           imgName:item.imgName,
           imgUrl:item.imgUrl,
           isDefault:item.isDefault,
           spuImgId:item.id,
         }
     });
     //发请求
     let result  = await this.$API.spu.reqAddSku(skuInfo);
     if(result.code==200){
        this.$message({type:'success',message:'添加SKU成功'})
        this.$emit('changeScenes',0);
     }
    }
  },
};
</script>
    
<style scoped>
</style>


