/**
* 头部菜单
*/ 
<template>
  <el-menu class="el-menu-demo" mode="horizontal" background-color="#334157" text-color="#fff" active-text-color="#fff">
    <el-button class="buttonimg">
      <img class="showimg" :src="collapsed?imgsq:imgshow" @click="toggle(collapsed)">
    </el-button>
    <el-submenu index="2" class="submenu">
      <template slot="title">{{user.loginName}}</template> 
      <!-- <template slot="title">超级管理员</template> -->
      <el-menu-item index="2-1">设置</el-menu-item>
      <el-menu-item @click="content()" index="2-2">个人中心</el-menu-item>
      <el-menu-item @click="exit()" index="2-3">退出</el-menu-item>
    </el-submenu>
  </el-menu>
</template>
<script>
import { loginout } from "../api/userMG";
export default {
  name: "navcon",
  data() {
    return {
      collapsed: true,
      imgshow: require("../assets/img/show.png"),
      imgsq: require("../assets/img/sq.png"),
      user: {},
      loginout: {
        loginToken: ""
      }
    };
  },
  // 创建完毕状态(里面是操作)
  created() {
    this.user = JSON.parse(localStorage.getItem("userdata"));
  },

  methods: {
    // 退出登录
    exit() {
      this.loginout.loginToken = localStorage.getItem("logintoken");
      loginout(this.loginout)
        .then(res => {
          if (res.code == 200) {
            //如果请求成功就让他2秒跳转路由

            this.$store.commit("logout", "false");
            this.$router.replace({ path: "/login" });
            localStorage.removeItem('logintoken');
            return true;
          } else {
            this.$message.error(res.msg);
            this.logining = false;
            return false;
          }
        })
        .catch(err => {
          // 获取图形验证码
          //this.getcode();
          this.logining = false;
          this.$message.error("退出失败，请稍后再试！");
        });
    },
    // 切换显示
    toggle(showtype) {
      this.collapsed = !showtype;
      this.$root.Bus.$emit("toggle", this.collapsed);
    }
  }
};
</script>
<style scoped>
.el-menu-vertical-demo:not(.el-menu--collapse) {
  border: none;
}
.submenu {
  float: right;
}
.buttonimg {
  height: 60px;
  background-color: transparent;
  border: none;
}
.showimg {
  width: 26px;
  height: 26px;
  position: absolute;
  top: 17px;
  left: 17px;
}
.showimg:active {
  border: none;
}
</style>