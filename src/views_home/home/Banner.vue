<template>
  <div class="banner-box">
    <div class="logo-img">logo</div>
    <div class="search-box">
      <el-input v-model="keyword" placeholder="请输入关键字..." class="keyword" />
      <el-button type="primary" class="btn">搜索</el-button>
    </div>
  </div>
</template>

<script>
  export default {
    name: 'Banner',
    data() {
      return {
        keyword: ''
      }
    }
  }
</script>

<style scoped>
  .banner-box{width:1200px;margin:auto;height:150px;display: flex;align-items: center;}
  .logo-img{width:120px;height:60px;line-height:60px;text-align:center;background: #cecece;}
  .search-box{margin-left:auto;}
  .keyword{width:260px;}
  .btn{}
</style>
