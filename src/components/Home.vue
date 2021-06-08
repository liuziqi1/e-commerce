<template>
  <div>
    <el-container class="home_container">
      <el-header>
        <div class="logotext">
          <img src="../assets/外星人.png" alt="" />
          <span>电商后台管理系统</span>
        </div>
        <el-button type="info" @click="logout">退出</el-button>
      </el-header>
      <el-container>
        <el-aside width="200px">
          <el-menu
            router
            unique-opened
            background-color="rgb(87, 85, 85)"
            active-text-color="#409EFF"
            text-color="#fff"
          >
            <!-- 一级菜单 -->
            <el-submenu
              :index="item.id + ''"
              v-for="item in menulist"
              :key="item.id"
            >
              <template slot="title">
                <i :class="iconsObj[item.id]"></i>
                <span>{{ item.authName }}</span>
              </template>
              <!-- 二级菜单 -->
              <el-menu-item
                :index="'/' + subItem.path"
                v-for="subItem in item.children"
                :key="subItem.id"
              >
                <template slot="title">
                  <i class="el-icon-menu"></i>
                  <span>{{ subItem.authName }}</span>
                </template>
              </el-menu-item>
            </el-submenu>
          </el-menu>
        </el-aside>
        <el-main>
          <router-view> </router-view>
        </el-main>
      </el-container>
    </el-container>
  </div>
</template>



<script>
export default {
  created() {
    this.getMenuList();
  },
  data() {
    return {
      menulist: [],
      iconsObj: {
        125: "iconfont iconManagement",
        103: "iconfont iconquanxianguanli",
        101: "iconfont iconshangpinguanli",
        102: "iconfont icondingdanguanli",
        145: "iconfont iconshujutongji",
      },
    };
  },
  methods: {
    logout() {
      window.sessionStorage.clear();
      this.$router.push("/login");
    },
    async getMenuList() {
      const { data: res } = await this.$http.get("menus");
      if (res.meta.status !== 200) return;

      this.menulist = res.data;
    },
  },
};
</script>



<style lang="less" scoped>
.home_container {
  height: 100% !important;
  .el-header {
    display: flex;
    justify-content: space-between;
    height: 80px !important;
    background-color: rgb(78, 80, 87);
    .logotext {
      display: flex;

      img {
        height: 50px;
        width: 50px;
        border-radius: 25px;
        background-color: #fff;
        margin-top: 15px;
      }
      span {
        line-height: 80px;
        margin-left: 10px;
        color: #fff;
        font-size: 20px;
      }
    }
    .el-button {
      height: 50px !important;
      margin: 15px;
    }
  }
  .el-container {
    height: 650px;
    .el-aside {
      background-color: rgb(87, 85, 85);
      .el-menu {
        .el-submenu {
          width: 200px !important;
          .iconfont {
            margin-right: 10px;
          }
        }
      }
    }
    .el-main {
    }
  }
}
</style>