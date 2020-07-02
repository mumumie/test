<template>
  <div class="container">
    <Header />
    <Banner />
    <nav-bar />
    <div class="main">
      <el-carousel :interval="5000" class="carousel-box">
        <el-carousel-item v-for="(item, index) in imgList" :key="index">
          <img :src="item" alt="">
        </el-carousel-item>
      </el-carousel>
    </div>

  </div>
</template>

<script>
import img1 from '@/assets/images/1.jpg'
import img2 from '@/assets/images/2.jpg'
import img3 from '@/assets/images/3.jpg'
export default {
  name: 'Home',
  components: {
    Header: () => import('./Header'),
    Banner: () => import('./Banner'),
    navBar: () => import('./navBar'),
  },
  data() {
    return {
      imgList: [img1, img2, img3]
    }
  },
  created() {
    // this.getInfo()
  },
  methods: {
    getInfo() {
      const postData = {
        typeName: 'Device',
        pagesize: 10,
        pageno: 0
      }
      this.$ajax.vpost('/queryBean', postData).then(res => {
        console.log(res)
      }).catch(() => {})
    }
  }
}
</script>

<style scoped>
  .main{width:1200px;margin:20px auto;}
  .carousel-box /deep/ .el-carousel__container{height:400px;}
  .carousel-box .el-carousel__item img{
    width:100%;
    height:100%;
  }
</style>
