<template>
  <div class="navbar-box">
    <el-menu
      :default-active="activeIndex"
      class="el-menu-demo"
      mode="horizontal"
      router
      @select="handleSelect"
      background-color="#46a6ff"
      text-color="#fff"
      active-text-color="#fff">
      <el-menu-item index="/">首页</el-menu-item>
      <el-menu-item index="/product-list">供方产品</el-menu-item>
      <el-menu-item index="/product-list">需方产品</el-menu-item>
      <!--      <el-submenu index="2">-->
      <!--        <template slot="title">供方</template>-->
      <!--        <el-menu-item index="2-1">选项1</el-menu-item>-->
      <!--        <el-menu-item index="2-2">选项2</el-menu-item>-->
      <!--        <el-menu-item index="2-3">选项3</el-menu-item>-->
      <!--        <el-submenu index="2-4">-->
      <!--          <template slot="title">选项4</template>-->
      <!--          <el-menu-item index="2-4-1">选项1</el-menu-item>-->
      <!--          <el-menu-item index="2-4-2">选项2</el-menu-item>-->
      <!--          <el-menu-item index="2-4-3">选项3</el-menu-item>-->
      <!--        </el-submenu>-->
      <!--      </el-submenu>-->
    </el-menu>
  </div>
</template>

<script>
  export default {
    name: 'NavBar',
    data() {
      return {
        activeIndex: '/'
      };
    },
    methods: {
      handleSelect(key, keyPath) {
        console.log(key, keyPath);
      }
    }
  }
</script>

<style scoped>
  .navbar-box{background:#46a6ff;height:50px; }
  .el-menu-demo{width:1200px;margin:0 auto;}
  .el-menu--horizontal > .el-menu-item{line-height: 50px;height:50px;font-size: 16px;min-width: 100px;text-align: justify;text-align-last:justify;}
</style>
