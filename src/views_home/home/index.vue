<template>
  <div class="container">
    <Header />
    <Banner :class-active="1" />
    <div class="banner-img" />
    <div class="main">
      <product-list :list="list" />
      <product-list :list="list" mode="circle" name="需方产品" path="/demand-list" />
      <!--      <el-carousel :interval="5000" class="carousel-box">-->
      <!--        <el-carousel-item v-for="(item, index) in imgList" :key="index">-->
      <!--          <img :src="item" alt="">-->
      <!--        </el-carousel-item>-->
      <!--      </el-carousel>-->
    </div>

  </div>
</template>

<script>
// import img1 from '@/assets/images/1.jpg'
// import img2 from '@/assets/images/2.jpg'
// import img3 from '@/assets/images/3.jpg'
export default {
  name: 'Home',
  components: {
    Header: () => import('./Header'),
    Banner: () => import('./Banner'),
    ProductList: () => import('./productList')
  },
  data() {
    return {
      // imgList: [img1, img2, img3]
      list: []
    }
  },
  created() {
    this.getInfo()
  },
  methods: {
    getInfo() {
      const postData = {
        typeName: 'ProductType',
        pagesize: 4,
        pageno: 0
      }
      this.$ajax.vpost('/queryBean', postData).then(res => {
        this.list = res.bean.data
      }).catch(() => {})
    }
  }
}
</script>

<style scoped>
  .banner-img{width:100%;min-width: 1200px;background: url(../../assets/images/banner.jpg) no-repeat center center;height:390px;}
  .main{width:1200px;margin:20px auto;}
  .carousel-box /deep/ .el-carousel__container{height:400px;}
  .carousel-box .el-carousel__item img{
    width:100%;
    height:100%;
  }
</style>
