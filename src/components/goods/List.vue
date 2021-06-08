<template>
  <div>
    <!-- 面包屑导航区域 -->
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>商品管理</el-breadcrumb-item>
      <el-breadcrumb-item>商品列表</el-breadcrumb-item>
    </el-breadcrumb>

    <!-- card -->
    <el-card>
      <!-- 搜索区域 -->
      <el-row :gutter="20">
        <el-col :span="8">
          <el-input v-model="queryinfo.query" clearable @clear="getGoodsList">
            <el-button
              slot="append"
              icon="el-icon-search"
              @click="getGoodsList"
            ></el-button>
          </el-input>
        </el-col>
        <el-col :span="4">
          <el-button type="primary" @click="goAddpage">添加商品</el-button>
        </el-col>
      </el-row>
      <!-- table表格区域 -->
      <el-table :data="goodslist" border stripe>
        <el-table-column type="index"></el-table-column>
        <el-table-column label="商品名称" prop="goods_name"></el-table-column>
        <el-table-column
          label="商品价格(元)"
          prop="goods_price"
          width="95px"
        ></el-table-column>
        <el-table-column
          label="商品重量"
          prop="goods_weight"
          width="70px"
        ></el-table-column>
        <el-table-column label="创建时间" prop="add_time" width="140px">
          <template slot-scope="scope">
            {{ scope.row.add_time | dateFormat }}
          </template>
        </el-table-column>
        <el-table-column label="操作" width="130px">
          <template slot-scope="scope">
            <el-button
              type="primary"
              icon="el-icon-edit"
              size="mini"
              @click="showEditDialog(scope.row.goods_id)"
            ></el-button>
            <el-button
              type="danger"
              icon="el-icon-delete"
              size="mini"
              @click="removeListById(scope.row.goods_id)"
            ></el-button>
          </template>
        </el-table-column>
      </el-table>
      <!-- 分页区域 -->
      <el-pagination
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :current-page="queryinfo.pagenum"
        :page-sizes="[5, 10, 15, 20]"
        :page-size="queryinfo.pagesize"
        layout="total, sizes, prev, pager, next, jumper"
        :total="total"
        background
      >
      </el-pagination>
    </el-card>

    <!-- 修改用户对话框 -->
    <el-dialog
      @close="editDialogClose"
      title="修改用户"
      :visible.sync="editDialogVisible"
      width="50%"
    >
      <el-form
        :model="editForm"
        :rules="editFormRules"
        ref="editFormRef"
        label-width="70px"
      >
        <el-form-item label-width="100px" label="商品名称" prop="goods_name">
          <el-input v-model="editForm.goods_name"></el-input>
        </el-form-item>
        <el-form-item label-width="100px" label="商品价格" prop="goods_name">
          <el-input v-model="editForm.goods_price"></el-input>
        </el-form-item>
        <el-form-item label-width="100px" label="商品数量" prop="goods_name">
          <el-input v-model="editForm.goods_number"></el-input>
        </el-form-item>
      </el-form>
      <el-button @click="editDialogVisible = false">取 消</el-button>
      <el-button type="primary" @click="editList">确 定</el-button>
    </el-dialog>
  </div>
</template>
       


<script>
export default {
  created() {
    this.getGoodsList();
  },
  data() {
    return {
      goodslist: [],
      queryinfo: {
        query: "",
        pagenum: 1,
        pagesize: 5,
      },
      total: 0,
      editForm: {},
      editFormRules: {
        goods_name: [
          { required: true, message: "请输入商品名称", trigger: "blur" },
          {
            min: 2,
            max: 50,
            message: "商品名称的长度在2~50个字符之间",
            trigger: "blur",
          },
        ],
        goods_price: [
          { required: true, message: "请输入商品价格", trigger: "blur" },
          {
            min: 2,
            max: 50,
            message: "商品名称的长度在2~50个字符之间",
            trigger: "blur",
          },
        ],
        goods_number: [
          { required: true, message: "请输入商品数量", trigger: "blur" },
          {
            min: 2,
            max: 50,
            message: "商品名称的长度在2~50个字符之间",
            trigger: "blur",
          },
        ],
      },
      editDialogVisible: false,
    };
  },
  methods: {
    async getGoodsList() {
      const { data: res } = await this.$http.get("goods/", {
        params: this.queryinfo,
      });
      if (res.meta.status !== 200) {
        return this.$message.error("获取列表失败");
      }
      this.goodslist = res.data.goods;
      this.total = res.data.total;
      this.$message.success("获取列表成功");
    },
    handleSizeChange(newSize) {
      this.queryInfo.pagesize = newSize;
      this.getGoodsList();
    },
    handleCurrentChange(newPage) {
      this.queryInfo.pagenum = newPage;
      this.getGoodsList();
    },
    async showEditDialog(id) {
      // console.log(id)
      const { data: res } = await this.$http.get("goods/" + id);
      // console.log(res);
      if (res.meta.status !== 200) {
        return this.$message.error("查询用户信息失败！");
      }

      this.editForm = res.data;
      this.editDialogVisible = true;
    },
    editList() {
      this.$refs.editFormRef.validate(async (valid) => {
        if (!valid) return;
        const { data: res } = await this.$http.put(
          "goods/" + this.editForm.id,
          {
            goods_name: this.editForm.goods_name,
          }
        );
        if (res.meta.status !== 200) {
          this.$message.error("修改用户失败");
        }
        this.$message.success("修改用户成功");
        this.editDialogVisible = false;
        this.getGoodsList();
      });
    },
    editDialogClose() {
      this.$refs.editFormRef.resetFields();
    },
    async removeListById(id) {
      const confirmResult = await this.$confirm(
        "此操作将永久删除该文件, 是否继续?",
        "提示",
        {
          confirmButtonText: "确定",
          cancelButtonText: "取消",
          type: "warning",
        }
      ).catch((err) => err);
      if (confirmResult !== "confirm") {
        return this.$message.info("已取消删除");
      }
      const { data: res } = await this.$http.delete("goods/" + id);

      if (res.meta.status !== 200) {
        return this.$message.error("删除失败");
      }
      this.$message.success("删除成功");
      this.getGoodsList();
    },
    goAddpage() {
      this.$router.push("/goods/add");
    },
  },
};
</script>


<style lang="less" scoped>
</style>