<template>
  <div>
    品牌管理
    <el-button
      type="primary"
      icon="el-icon-plus"
      style="margin: 10px 0px"
      @click="showDialog"
      >添加</el-button
    >
    <!-- table表格 
    el-table元素中注入data对象数组list=data.records，在每一列中用prop属性写入键值名称即可填入数据
    -->
    <el-table style="width: 100%" border :data="list">
      <el-table-column type="index" label="序号" width="80px" align="center">
      </el-table-column>
      <el-table-column prop="tmName" label="品牌名称" width="width">
      </el-table-column>
      <!-- elmentUI中预设了一项技能，使用插槽时，预设三个参数(row,column,$index) -->
      <el-table-column label="品牌logo" width="width">
        <!-- <el-table-column>可以使用作用域插槽，插槽中需要定义一个接收数据的对象 -->
        <template slot-scope="{ row, $index }">
          <img :src="row.logoUrl" alt="" style="width: 100px; height: 100px" />
        </template>
      </el-table-column>
      <!-- 插入图片，图片来源于data数据中的row.imgURl -->

      <el-table-column label="品牌名称">
        <template slot-scope="{ row }">
          <img :src="row.logoUrl" style="width: 56px; height: 56px" />
        </template>
      </el-table-column>

      <el-table-column label="操作" >
        <template slot-scope="{ row }">
          <el-button
            type="warning"
            icon="el-icon-edit"
            size="mini"
            @click="updateTradeMark(row)"
            >修改</el-button
          >
          <el-button
            type="danger"
            icon="el-icon-delete"
            size="mini"
            @click="deleteTradeMark(row)"
            >删除</el-button
          >
        </template>
      </el-table-column>
      <el-table-column label="操作">
        <template>
          <el-button>很不错哦</el-button>
        </template>
      </el-table-column>
    </el-table>
    <el-pagination
      style="margin-top: 20px; textaalign: center"
      :current-page="page"
      :page-sizes="[3, 5, 10]"
      :page-size="limit"
      :pager-count="7"
      :total="total"
      @size-change="handleSizeChange"
      @current-change="getPageList"
      layout="prev,pager,next,jumper,->,sizes,total"
    >
    </el-pagination>


<!-- el-dialog为对话框
    需要控制显示与隐藏的属性
     分为body和footer
 -->
 <!-- dialog中包含着表单，表单验证 其中:model和:rules控制着表单的校验 -->
    <el-dialog
      :title="tmForm.id ? '修改品牌' : '添加品牌'"
      :visible.sync="dialogFormVisible"
    >
      <el-form style="width: 80%" :model="tmForm" :rules="rules" ref="ruleForm">
        <el-form-item label="品牌名称" label-width="100px" prop="tmName">
          <el-input autocomplete="off" v-model="tmForm.tmName"></el-input>
        </el-form-item>
        <el-form-item label="品牌LOGO" label-width="100px" prop="logoUrl">
          <!-- action是上传图片的地址 -->
          <el-upload
            class="avatar-uploader"
            
            action="/admin/product/fileUpload"
            :show-file-list="false"
            :on-success="handleAvatarSuccess"
            :before-upload="beforeAvatarUpload"
          >
            <img v-if="tmForm.logoUrl" :src="tmForm.logoUrl" class="avatar" />
            <i v-else class="el-icon-plus avatar-uploader-icon"></i>
          </el-upload>
        </el-form-item>
      </el-form>

      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisible = false">取 消</el-button>
        <el-button type="primary" @click="AddOrUpdateTradeMark"
          >确 定</el-button
        >
      </div>
    </el-dialog>
  </div>
</template>

<script>
import { reqAddOrUpdateTradeMark } from "@/api/product/tradeMark";
export default {
  name: "tradeMark",
  data() {
    // 自定义校验规则，其中callback必须被使用
    // 有关validate校验的具体写法在elementUI中找到表单form下的自定义校验规则可以看到

   
    var validateTmName = (rule, value, callback) => {
      if (value.length < 2 || value.length > 10) {
        callback(new Error("品牌名称2-10位"));
      } else {
        callback();
      }
    };
    return {
      imageUrl: "",
      page: 1,
      limit: 3,
      total: 0,
      list: [],
      tmForm: {
        tmName: "",
        logoUrl: "",
      },
      dialogFormVisible: false,
      rules: {
        tmName: [
          { required: true, message: "请输入品牌名称", trigger: "blur" },
          // 改变的时候激活自定义校验规则
          { validator: validateTmName, trigger: "change" },
        ],
        logoUrl: [
          { required: true, message: "请选择品牌图片", trigger: "change" },
        ],
      },
    };
  },
  mounted() {
    //挂载拉数据
    this.getPageList();
  },
  methods: {
    // 拉数据，从接口传入参数
    async getPageList(pager = 1) {
      this.page = pager;

      // const page=this.page
      // const limit=this.limit
      // 解构
      const { page, limit } = this;
      let result = await this.$API.trademark.reqTradeMarkList(page, limit);

      console.log(result);
      // 打印之后观察result里面的数据，其中data.records里面是一个数组
      if (result.code == 200) {
        this.total = result.data.total;

        this.list = result.data.records;
      }
    },
    handleSizeChange(limit) {
      this.limit = limit;
      this.getPageList();
    },
    showDialog() {
      this.dialogFormVisible = true;
    },
    updateTradeMark(row) {
      this.dialogFormVisible = true;
      // 深拷贝row对象
      this.tmForm = { ...row };

    },
    handleAvatarSuccess(res, file) {
      this.imageUrl = URL.createObjectURL(file.raw);
    },
    beforeAvatarUpload(file) {
      const isJPG = file.type === "image/jpeg";
      const isLt2M = file.size / 1024 / 1024 < 2;

      if (!isJPG) {
        this.$message.error("上传头像图片只能是 JPG 格式!");
      }
      if (!isLt2M) {
        this.$message.error("上传头像图片大小不能超过 2MB!");
      }
      return isJPG && isLt2M;
    },

    // 配套表单校验中应有的提交之前的validate
    AddOrUpdateTradeMark() {
      this.$refs.ruleForm.validate(async (success) => {
        if (success) {
          //关闭对话框
          this.dialogFormVisible = false;
          // 传输参数到接口，接收相应的数据
          let result = await this.$API.trademark.reqAddOrUpdateTradeMark(
            this.tmForm
          );
          // 检查返回的结果，若返回200成功则弹出对话框修改成功
          if (result.code == 200) {
            this.$message({
              type: "success",
              message: this.tmForm.id ? "修改品牌成功" : "添加品牌成功",
            });
            this.getPageList(this.tmForm.id ? this.page : 1);
          }
        }
      });
    },

    // ElementUI组件中MessageBox 弹框
    deleteTradeMark(row) {
      this.$confirm(`你确定删除 ${row.tmName}?`, "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning",
      })
        .then(async () => {
          // 确认删除即将对应行的id传入接口然后获得其返回值，若返回值为200成功则删除成功
          let result = await this.$API.trademark.reqDeleteTradeMark(row.id);
          if (result.code == 200) {
            this.$message({
              type: "success",
              message: "删除成功!",
            });
            // this.list,length是响应式的数据
            this.getPageList(this.list.length > 1 ? this.page : this.page - 1);
          }
        })
        .catch(() => {
          this.$message({
            type: "info",
            message: "已取消删除",
          });
        });
    },
  },
};
</script>

<style>
.avatar-uploader .el-upload {
  border: 1px dashed #d9d9d9;
  border-radius: 6px;
  cursor: pointer;
  position: relative;
  overflow: hidden;
}
.avatar-uploader .el-upload:hover {
  border-color: #409eff;
}
.avatar-uploader-icon {
  font-size: 28px;
  color: #8c939d;
  width: 178px;
  height: 178px;
  line-height: 178px;
  text-align: center;
}
.avatar {
  width: 178px;
  height: 178px;
  display: block;
}
</style>