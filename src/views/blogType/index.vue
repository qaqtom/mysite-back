<template>
  <div class="app-container">
    <!-- 搜索框 -->
    <div style="margin-top: 15px">
      <el-input
        v-model="input"
        placeholder="请输入内容"
        class="input-with-select blogAddInput"
      >
        <el-select v-model="select" placeholder="请选择" slot="prepend">
          <el-option label="1" value="1"></el-option>
          <el-option label="2" value="2"></el-option>
          <el-option label="3" value="3"></el-option>
          <el-option label="4" value="4"></el-option>
          <el-option label="5" value="5"></el-option>
        </el-select>
      </el-input>
      <el-button
        type="primary"
        style="margin-left: 10px"
        @click="addBlogTypeHandle"
        >添加</el-button
      >
    </div>

    <!-- 表格 -->
    <el-table :data="data" style="width: 100%" border v-loading="listLoading">
      <!-- 序号 -->
      <el-table-column label="序号" width="60" align="center">
        <template slot-scope="scope">
          {{ scope.$index + 1 }}
        </template>
      </el-table-column>
      <!-- 博客类别 -->
      <el-table-column label="博客类别" align="center">
        <template slot-scope="scope">{{ scope.row.name }}</template>
      </el-table-column>
      <!-- 文章数量 -->
      <el-table-column label="文章数量" align="center">
        <template slot-scope="scope">{{ scope.row.articleCount }}</template>
      </el-table-column>

      <el-table-column label="操作" align="center" width="150">
        <template slot-scope="scope">
          <!-- 编辑 -->
          <el-tooltip
            class="item"
            effect="dark"
            content="编辑"
            placement="top"
            :hide-after="2000"
          >
            <el-button
              type="primary"
              icon="el-icon-edit"
              circle
              size="mini"
              @click="editBlogTypeHandle(scope.row)"
            ></el-button>
          </el-tooltip>
          <!-- 删除 -->
          <el-tooltip
            class="item"
            effect="dark"
            content="删除"
            placement="top"
            :hide-after="2000"
          >
            <el-button
              type="danger"
              icon="el-icon-delete"
              circle
              size="mini"
              @click="deleteBlogTypeHandle(scope.row)"
            ></el-button>
          </el-tooltip>
        </template>
      </el-table-column>
    </el-table>

    <!-- 编辑文章分类 弹出框 -->
    <el-dialog title="编辑文章分类" :visible.sync="dialogFormVisible">
      <el-form :model="form">
        <el-form-item label="分类名称">
          <el-input v-model="form.name"></el-input>
        </el-form-item>
        <el-form-item label="排序等级">
          <el-select v-model="form.order" placeholder="分类等级">
            <el-option label="1" value="1"></el-option>
            <el-option label="2" value="2"></el-option>
            <el-option label="3" value="3"></el-option>
            <el-option label="4" value="4"></el-option>
            <el-option label="5" value="5"></el-option>
          </el-select>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisible = false">取 消</el-button>
        <el-button type="primary" @click="confirmEditBlogTypeHandle"
          >确 定</el-button
        >
      </div>
    </el-dialog>
  </div>
</template>

<script>
import {
  getBlogType,
  addBlogType,
  delBlogType,
  findOneBlogType,
  updateOneBlogType,
} from "@/api/blogType.js";
export default {
  data() {
    return {
      input: "",
      select: "",
      data: [],
      listLoading: false,
      dialogFormVisible : false,
      form : {
          name : '',
          order : ''
      }
    };
  },
  created() {
    this.fetchData();
  },
  methods: {
    async fetchData() {
      this.listLoading = true;
      this.data = (await getBlogType()).data;
      this.listLoading = false;
    },
    //添加文章分分类
    async addBlogTypeHandle() {
      if (this.input) {
        await addBlogType({ name: this.input, order: this.select });
        this.fetchData();
        this.$message.success("添加分类成功");
      } else {
        this.$message.error("分类名称不能为空");
      }
    },

    //编辑文章分类
    editBlogTypeHandle({id}) {
        findOneBlogType(id).then(resp=>{
            this.form = resp.data;
            this.dialogFormVisible = true;
        })
    },

    //删除文章分类
    deleteBlogTypeHandle({id}) {
        this.$confirm(
        "删除该分类后，该分类下面的所有文章将变为未分类状态，是否继续?",
        "是否删除此文章分类",
        {
          confirmButtonText: "确定",
          cancelButtonText: "取消",
          type: "warning",
        }
      )
        .then(() => {
          delBlogType(id).then(() => {
            this.fetchData();
            this.$message.success("删除成功");
          });
        })
        .catch(() => {
          this.$message({
            type: "info",
            message: "已取消删除",
          });
        });
    },

    // 确认编辑文章分类
    confirmEditBlogTypeHandle(){
        updateOneBlogType({
            id : this.form.id,
            data : this.form
        }).then(()=>{
            this.fetchData();
            this.$message.success('更新分类信息成功');
            this.dialogFormVisible = false;
        })
    },
  },
};
</script>

<style lang="scss" scoped>
.blogAddInput {
  width: 400px;
  margin-bottom: 20px;
}
</style>