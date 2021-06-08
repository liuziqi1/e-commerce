<template>
  <div class="login_container">
    <div class="login_box">
      <!-- 头像区 -->
      <div class="logo">
        <img src="../assets/logo.png" alt="" />
      </div>

      <!-- 登录表单区域 -->
      <el-form
        ref="loginFormRef"
        class="login_form"
        :model="loginForm"
        :rules="loginFormRules"
      >
        <!-- 用户名 -->
        <el-form-item prop="username">
          <el-input
            v-model="loginForm.username"
            prefix-icon="iconfont iconyonghu"
          ></el-input>
        </el-form-item>
        <!-- 密码 -->
        <el-form-item prop="password">
          <el-input
            show-password
            v-model="loginForm.password"
            prefix-icon="iconfont iconmima"
          ></el-input>
        </el-form-item>
        <!-- 按钮区域 -->
        <el-form-item class="btns">
          <el-button @click="login" type="primary">登录</el-button>
          <el-button @click="resetLoginForm" type="info">重置</el-button>
        </el-form-item>
      </el-form>
    </div>
  </div>
</template>




<script>
export default {
  data() {
    return {
      loginForm: {
        username: "admin",
        password: "123456",
      },
      loginFormRules: {
        username: [
          {
            required: true,
            message: "请输入活动名称",
            trigger: "blur",
          },
          {
            min: 3,
            max: 10,
            message: "长度在 3 到 10 个字符",
            trigger: "blur",
          },
        ],
        password: [
          {
            required: true,
            message: "请输入密码",
            trigger: "blur",
          },
          {
            min: 6,
            max: 12,
            message: "长度在 6 到 12 个字符",
            trigger: "blur",
          },
        ],
      },
    };
  },
  methods: {
    resetLoginForm() {
      this.$refs.loginFormRef.resetFields();
    },
    login() {
      this.$refs.loginFormRef.validate(async (valid) => {
        if (!valid) return;
        const { data: res } = await this.$http.post("login", this.loginForm);
        if (res.meta.status !== 200) return this.$message.error("登录失败");
        this.$message.success("登录成功");
        // console.log(res);
        window.sessionStorage.setItem("token", res.data.token);
        // 保存token令牌
        this.$router.push("/home");
      });
    },
  },
};
</script>



<style lang="less" scoped>
.login_container {
  position: relative;
  height: 100%;
  background-color: rgb(66, 65, 65);
  .login_box {
    position: absolute;
    left: 50%;
    top: 50%;
    transform: translate(-50%, -50%);
    width: 450px;
    height: 300px;
    background-color: #fff;
    border-radius: 5px;
    .logo {
      position: absolute;
      left: 50%;
      transform: translate(-50%, -50%);
      width: 100px;
      height: 100px;
      background-color: #fff;
      border-radius: 50px;
      box-shadow: 0 0 7px;
      img {
        display: block;
        width: 85px;
        height: 85px;
        margin: 0 auto;
      }
    }
    .login_form {
      margin-top: 100px;
      .el-form-item {
        padding: 0 30px;
      }
      .btns {
        margin: 0;
        margin-top: 30px;
        padding-left: 270px;
      }
    }
  }
}
</style>