<template>
  <div class="container">
    <Header title="产品详情" />
    <div class="main">
      <div class="title">
        {{ productInfo.name }}
      </div>
      <table class="content">
        <tr>
          <td class="bg">产品名称</td>
          <td>{{ productInfo.name }}</td>
          <td class="bg">产品长度a</td>
          <td>{{ productInfo.length }}</td>
        </tr>
        <tr>
          <td class="bg">产品宽度b</td>
          <td>{{ productInfo.width }}</td>
          <td class="bg">产品厚度H</td>
          <td>{{ productInfo.height }}</td>
        </tr>
        <tr>
          <td class="bg">a边伸筋(0,1)</td>
          <td>{{ productInfo.absj }}</td>
          <td class="bg">b边伸筋(0,1)</td>
          <td>{{ productInfo.bbsj }}</td>
        </tr>
      </table>
    </div>
  </div>
</template>

<script>
export default {
  name: 'ProductDetail',
  components: {
    Header: () => import('../home/Header')
  },
  data() {
    return {
      productInfo: {},
      productType: ['-', '钢筋', '水泥', '设备']
    }
  },
  created() {
    this.getProduct()
  },
  methods: {
    getProduct() {
      const params = {
        typeName: this.$route.query.typeName,
        id: this.$route.query.id
      }
      this.$ajax.vpost('getBean', params).then(res => {
        console.log(res)
        this.productInfo = res.bean
      })
    }
  }
}
</script>

<style scoped>
  .main{width:1200px;margin: 30px auto;}
  .title{line-height: 50px;font-size: 23px;font-weight: bold;text-align: center;}
  .content{width:100%;border-top:1px solid  #eee;border-left:1px solid #eee;border-collapse: collapse;border-spacing: 0;margin-top:10px;}
  .content td.bg{background: #f8f8f8;width:130px;text-align: center;}
  .content td{padding: 12px; line-height:28px;border-bottom:1px solid #eee;border-right:1px solid #eee;}
</style>
