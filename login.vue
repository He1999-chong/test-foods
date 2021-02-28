<!-- 组件说明 -->
<template>
  <div class="box">
    <el-card class="box-card">
      <div class="login-wrap">
        <el-form
          class="login-container"
          :rules="rules"
          ref="admin"
          :model="admin"
        >
          <h3 class="title">点餐系统管理员登录</h3>
          <el-form-item label="账号" prop="name">
            <el-input
              type="text"
              v-model="admin.name"
              autocomplete="off"
            ></el-input>
          </el-form-item>
          <el-form-item label="密码" prop="password">
            <el-input
              type="password"
              v-model="admin.password"
              autocomplete="off"
            ></el-input>
          </el-form-item>
          <el-form-item>
            <el-button
              type="primary"
              style="width: 100%"
              @click="doSubmit('admin')"
              >登录</el-button
            >
          </el-form-item>
        </el-form>
      </div>
    </el-card>
  </div>
</template>

<script>
//import x from ''
import { login } from "@/api/admin.js"
export default {
  components: {},
  data() {
    return {
      admin: {
        name: "",
        password: "",
      },
      rules: {
        name: [
          { required: true, message: "请输入账号", trigger: "blur" },
        ],
        password: [{ required: true, message: "请输入密码", trigger: "blur" }],
      },
    };
  },
  computed: {},
  methods: {
    doSubmit(formName) {
      this.$refs[formName].validate((valid) => {
        if (valid) {
          login({
            'name':this.admin.name,
            'password':this.admin.password
          }).then((res) => {
            console.log(res);
            if (res.data.code === 0) {
              var admin = res.data.data;
              console.log(admin);
              this.$message({
                showClose: true,
                message: "欢迎登录，" + admin.name,
                type: "success",
              });
              localStorage.setItem("admin", JSON.stringify(admin));
              this.$router.push("/admin");
            } else {
              this.$message({
                showClose: true,
                message: res.data.msg,
                type: "warning",
              });
            }
          });
        } else {
          this.$message({
            showClose: true,
            message: "不能为空",
            type: "error",
          });
          return false;
        }
      });
    },
  },
  created() {}, //生命周期 - 创建完成(可以访问当前this实例)
  mounted() {}, //生命周期 - 挂载完成（可以访问DOM元素）
  beforeCreate() {}, //生命周期 - 创建之前
  beforeMount() {}, //生命周期 - 挂载之前
  beforeUpdate() {}, //生命周期 - 更新之前
  updated() {}, //生命周期 - 更新之后
  beforeDestroy() {}, //生命周期 - 销毁之前
  destroyed() {}, //生命周期 - 销毁完成
  activated() {}, //如果页面有keep-alive缓存功能，这个函数会触发
};
</script>

<style scoped>
.box {
  height: 100%;
  position: absolute;
  top: 0px;
  left: 0px;
  width: 100%;
  display: flex;
  justify-content: center;
  background-image: url(../../images/timg.jpg);
  background-repeat: no-repeat;
  background-size: 100% 100%;
  -moz-background-size: 100% 100%;
  align-items: center;
}
.box-card {
  width: 24%;
  opacity: 0.75;
}
.login-container {
  text-align: center;
}
.el-input {
  width: 82%;
  float: right;
}
</style>