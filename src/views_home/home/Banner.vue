<template>
  <div class="banner-box">
    <div class="logo-img">建筑建材公司</div>
    <div class="navbar-box">
      <div v-for="item in navInfo" :key="item.name" :class="{active: item.id === classActive}" @click="$router.push(item.path)">{{ item.name }}</div>
    </div>
    <!--    <div class="search-box">-->
    <!--      <el-input v-model="keyword" placeholder="请输入关键字..." class="keyword" />-->
    <!--      <el-button type="primary" class="btn" @click="searchBtn">搜索</el-button>-->
    <!--    </div>-->
  </div>
</template>

<script>
export default {
  name: 'Banner',
  props: {
    classActive: {
      type: Number,
      default: 1
    }
  },
  data() {
    return {
      keyword: '',
      navInfo: [
        { id: 1, name: '首页', path: '/' },
        { id: 2, name: '供方产品', path: '/product' },
        { id: 3, name: '需方产品', path: '/demand' },
        { id: 4, name: '关于我们', path: '/' },
        { id: 5, name: '联系我们', path: '/' }
      ]
    }
  },
  methods: {
    searchBtn() {
      this.$router.push({ path: '/product-list', query: { keyword: this.keyword }})
    }
  }
}
</script>

<style scoped>
  .banner-box{width:1200px;margin:auto;height:120px;display: flex;align-items: center;}
  .logo-img{height:60px;line-height:60px;text-align:center;font-size: 32px;font-weight: bold;}
  .navbar-box{display: flex;margin-left:auto;}
  .navbar-box > div {margin:0 10px;padding:0 5px;height:50px;line-height: 50px;font-size: 16px;position:relative;cursor: pointer;}
  .navbar-box > div.active, .navbar-box > div:hover{font-weight: bold;color:#1890ff;}
  .navbar-box > div.active:before, .navbar-box > div:hover:before{content:'';display: block;width:100%;height:3px;background: #1890ff;position:absolute;bottom:0;left:0;}
  .search-box{margin-left:auto;}
  .keyword{width:260px;}
  .btn{}
</style>
