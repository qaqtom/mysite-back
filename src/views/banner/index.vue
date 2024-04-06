<template>
  <div class="app-container">
    <el-table :data="data" style="width: 100%" border>
      <!-- 序号 -->
      <el-table-column label="序号" width="60" align="center">
        <template slot-scope="scope">
          {{ scope.$index + 1 }}
        </template>
      </el-table-column>
      <!-- 标题 -->
      <el-table-column label="标题" width="150" align="center">
        <template slot-scope="scope">
          {{ scope.row.title }}
        </template>
      </el-table-column>
      <!-- 描述 -->
      <el-table-column label="描述" width="330">
        <template slot-scope="scope">
          {{ scope.row.description }}
        </template>
      </el-table-column>

      <el-table-column label="中图预览" align="center">
        <template slot-scope="scope">
          <el-image style="width: 100px" :src="scope.row.midImg" fit="fill">
          </el-image>
        </template>
      </el-table-column>

      <el-table-column label="大图预览" align="center">
        <template slot-scope="scope">
          <el-image style="width: 100px" :src="scope.row.bigImg" fit="fill">
          </el-image>
        </template>
      </el-table-column>

      <el-table-column label="操作" align="center">
        <template slot-scope="scope">
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
              @click="editBannerHandle(scope.row)"
            ></el-button>
          </el-tooltip>
        </template>
      </el-table-column>
    </el-table>

    <!-- 嵌套的表单 -->
    <!-- Form -->

    <el-dialog title="请编辑信息" :visible.sync="dialogFormVisible" top="5vh">
      <el-form :model="form">
        <!-- 标题 -->
        <el-form-item label="标题">
          <el-input v-model="form.title"></el-input>
        </el-form-item>

        <!-- 描述 -->
        <el-form-item label="描述">
          <el-input v-model="form.description"></el-input>
        </el-form-item>

        <!-- //图片区域 -->
        <el-row>
          <el-col :span="12">
            <el-form-item label="首页中图">
              <Upload v-model="form.midImg" />
            </el-form-item>
          </el-col>
          <el-col :span="12">
            <el-form-item label="首页大图">
              <Upload v-model="form.bigImg" />
            </el-form-item>
          </el-col>
        </el-row>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisible = false">取 消</el-button>
        <el-button type="primary" @click="editBannerConfirm">确 定</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
import { getBanner, setBanner } from "@/api/banner.js";
import { server_URL } from "@/urlConfig.js";
import Upload from "@/components/Upload.vue";
export default {
  components: {
    Upload,
  },
  data() {
    return {
      data: [],
      form: {
        id: "",
        midImg: "",
        bigImg: "",
        title: "",
        description: "",
      },
      dialogFormVisible: false, // 编辑对话框是否显示
    };
  },
  created() {
    this.fetchData();
  },
  methods: {
    fetchData() {
      getBanner().then((res) => {
        this.data = res.data;
        // for (var item of this.data) {
        //   item.midImg2 = server_URL + item.midImg;
        //   item.bigImg2 = server_URL + item.bigImg;
        // }
      });
    },
    editBannerHandle(bannerInfo) {
      this.form = { ...bannerInfo };
      this.dialogFormVisible = true;
    },
    async editBannerConfirm() {
      let arr = [...this.data];
      arr = arr.map(obj => {
          if(obj.id === this.form.id){
              return this.form;
          }else{
              return obj;
          }
      });
    //   let arr = [...this.data];
    //   for (var i = 0; i < arr.length; i++) {
    //     if (arr[i].id == this.form.id) {
    //       arr[i] = this.form;
    //     }
    //   }
      await setBanner(arr);
      this.dialogFormVisible = false; // 关闭掉对话框
      this.$message({
        message: "修改成功",
        type: "success",
      });
      this.fetchData();
    },
  },
};
</script>

<style lang="scss" scoped>
</style>