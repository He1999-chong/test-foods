<!-- 组件说明 -->
<template>
  <div class="main">
    <el-container>
      <el-header>
        <el-menu
          class="top-menu"
          mode="horizontal"
          background-color="#545c64"
          text-color="#fff"
          active-text-color="#ffd04b"
        >
          <el-menu-item class="title">同城点餐系统后台</el-menu-item>

          <el-menu-item
            class="right"
            @click.native="handleClick"
            v-if="disLogin"
            >登录</el-menu-item
          >
          <el-menu-item index="4" class="right" v-if="!disLogin">
            <el-dropdown>
              <i class="el-icon-user-solid" style="margin-right: 15px"></i>
              <el-dropdown-menu slot="dropdown">
                <el-dropdown-item @click.native="handleClick"
                  >退出登录</el-dropdown-item
                >
              </el-dropdown-menu>
            </el-dropdown>
          </el-menu-item>
        </el-menu>
      </el-header>
      <el-container>
        <div class="left">
          <el-menu
            router
            :default-active="$route.path"
            background-color="#545c64"
            text-color="#fff"
            active-text-color="#ffd04b"
          >
            <el-menu-item index="/adminManage">
              <i class="el-icon-eleme"></i>
              <span slot="title">管理员管理</span>
            </el-menu-item>
            <!-- <el-menu-item index="/cart">
              <i class="el-icon-eleme"></i>
              <span slot="title">购物车管理</span>
            </el-menu-item> -->
            <el-menu-item index="/user">
              <i class="el-icon-eleme"></i>
              <span slot="title">用户管理</span>
            </el-menu-item>
            <el-menu-item index="/food">
              <i class="el-icon-eleme"></i>
              <span slot="title">菜品管理</span>
            </el-menu-item>
            <el-menu-item index="/aorder">
              <i class="el-icon-eleme"></i>
              <span slot="title">订单管理</span>
            </el-menu-item>
            <el-menu-item index="/slideshow">
              <i class="el-icon-eleme"></i>
              <span slot="title">轮播图管理</span>
            </el-menu-item>
          </el-menu>
        </div>
        <el-main>
          <router-view />
        </el-main>
      </el-container>
    </el-container>
  </div>
</template>

<script>
//import x from ''
export default {
  components: {},
  data() {
    return {
      disLogin: true,
    };
  },
  computed: {},
  methods: {
    handleClick(e) {
      this.disLogin = true;
      localStorage.clear();
      this.$router.push("/alogin");
    },
    init() {
      if(localStorage.getItem("admin") != null){
        var admin = JSON.parse(localStorage.getItem("admin"));
        if (admin != null) {
          this.disLogin = false;
        } else {
          this.disLogin = true;
        }
      }else{
        this.$router.push("/alogin");
      }
    },
  },
  created() {
    this.init();
  }, //生命周期 - 创建完成(可以访问当前this实例)
  mounted() {
    this.init();
  }, //生命周期 - 挂载完成（可以访问DOM元素）
  beforeCreate() {}, //生命周期 - 创建之前
  beforeMount() {}, //生命周期 - 挂载之前
  beforeUpdate() {}, //生命周期 - 更新之前
  updated() {
    this.init();
  }, //生命周期 - 更新之后
  beforeDestroy() {}, //生命周期 - 销毁之前
  destroyed() {}, //生命周期 - 销毁完成
  activated() {}, //如果页面有keep-alive缓存功能，这个函数会触发
};
</script>

<style scoped>
.main {
  width: 100%;
  height: 100%;
}
.el-container {
  width: 100%;
  height: 100%;
}
.el-header {
  padding: 0px;
  height: 200px;
}
.left {
  width: 200px;
  height: 100%;
}
.left .el-menu {
  height: 100%;
}
.title {
  font-size: 18px;
  font-weight: bold;
  color: cornflowerblue;
}
.right {
  float: right;
}
</style>