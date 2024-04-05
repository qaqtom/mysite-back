<template>
  <div class="app-container">
    <el-form
      :model="adminInfo"
      :rules="setRules"
      ref="ruleForm"
      label-width="100px"
      width="500px"
      class="form"
    >
      <el-form-item label="用户名">
        <el-input
          v-model="adminInfo.name"
          placeholder="请输入用户名"
        ></el-input>
      </el-form-item>

      <el-form-item label="旧密码" prop="oldLoginPwd">
        <el-input
          v-model="adminInfo.oldLoginPwd"
          placeholder="请输入旧密码"
          type="password"
        ></el-input>
      </el-form-item>

      <el-form-item label="新密码" prop="loginPwd">
        <el-input
          v-model="adminInfo.loginPwd"
          placeholder="请输入新密码"
          type="password"
        ></el-input>
      </el-form-item>

      <el-form-item label="新密码确认" prop="loginPwdConfirm">
        <el-input
          v-model="adminInfo.loginPwdConfirm"
          placeholder="请确认新密码"
          type="password"
        ></el-input>
      </el-form-item>

      <div class="button">
        <el-button type="primary" style="margin-top: 15px" @click="handleClick"
          >修改</el-button
        >

        <el-button type="danger" style="margin-top: 15px" @click="handleBack"
          >返回</el-button
        >
      </div>
    </el-form>
  </div>
</template>

<script>
import { getInfo, setUser } from "@/api/user.js";
export default {
  data() {
    var validatePass2 = (rule, value, callback) => {
      if (value === "") {
        callback(new Error("请再次输入新密码"));
      } else if (value !== this.adminInfo.loginPwd) {
        callback(new Error("两次输入密码不一致!"));
      } else {
        callback();
      }
    };

    return {
      url: "",
      adminInfo: {
        id: "",
        loginId: "", // loginId
        name: "", // 用户名
        oldLoginPwd: "", // 旧密码
        loginPwd: "", // 新密码
        loginPwdConfirm: "", // 密码确认
      },
      setRules: {
        oldLoginPwd: [
          { required: true, trigger: "blur", message: "请输入旧的密码" },
        ],
        loginPwd: [
          { required: true, trigger: "blur", message: "请输入新的密码" },
        ],
        loginPwdConfirm: [
          { required: true, trigger: "blur", message: "请再次输入新密码" },
          { validator: validatePass2, trigger: "blur" },
        ],
      },
    };
  },
  created() {
    this.fetchData();
  },
  methods: {
    fetchData() {
      getInfo().then((res) => {
        this.adminInfo = res.data;
        console.log(res);
      });
      console.log(this.adminInfo);
    },
    handleBack() {
      this.$router.push("/");
    },
    handleClick() {
      // 处理修改的逻辑
      if (
        this.adminInfo.name &&
        this.adminInfo.loginPwd &&
        this.adminInfo.oldLoginPwd
      ) {
        if (this.adminInfo.oldLoginPwd === this.adminInfo.loginPwd) {
          this.$message.error("新密码不能与旧密码相同");
        } else if (this.adminInfo.loginPwd !== this.adminInfo.loginPwdConfirm) {
          this.$message.error("两次输入密码不一致");
        } else {
          setUser(this.adminInfo).then((res) => {
            if (typeof res === "string") {
              const resp = JSON.parse(res);
              this.$message.error(resp.msg);
            } else {
              // 说明修改成功
              this.$message.success("修改密码成功");
              this.$store.dispatch("user/logout").then(() => {
                this.$router.push(`/login?redirect=${this.$route.fullPath}`);
              });
            }
          });
        }
      } else {
        this.$message.error("请填写所有的项目");
      }
    },
  },
};
</script>

<style lang="scss" scoped>
.app-container {
  width: 500px;
}
.form {
  position: relative;
}
.button {
  position: absolute;
  right: 0;
}
</style>