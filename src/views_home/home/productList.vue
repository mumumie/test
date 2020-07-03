<template>
  <div class="product-box">
    <div class="title">
      <span>{{ name }}</span>
    </div>
    <div class="content">
      <div v-for="(item, index) in list" :key="index" :class="['list-box', mode]">
        <div class="message" @click="selectHandle(item)">
          <div class="img">
            <img :src="item.picList[0]" alt="">
          </div>
          <p>{{ item.name }}</p>
          <div class="abstract">具有良好的可加工性、耐大气腐蚀性，并且可以耐受许多有机与无机化合物。</div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'ProductList',
  props: {
    name: {
      type: String,
      default: '产品中心'
    },
    mode: {
      type: String,
      default: 'square'
    },
    path: {
      type: String,
      default: '/product-list'
    },
    list: {
      type: Array
    }
  },
  methods: {
    selectHandle(row) {
      this.$router.push({ path: this.path, query: { type: row.id }})
    }
  }
}
</script>

<style scoped>
  .product-box{margin-bottom:30px;}
  .title{height:45px;line-height: 45px;border-bottom:1px solid #eee;font-size: 16px;font-weight: 600;}
  .title span{display: block;width: 90px;text-align: center;position:relative;color:#1890ff;}
  .title span:before{content: '';display: block;width:100%;height:3px;position:absolute;bottom:0;left:0;background: #1890ff;}
  .content{margin-top:20px;display: flex;}
  .list-box{width:25%;}
  .list-box > div.message{padding:10px 20px;cursor:pointer;}
  .list-box > div.message:hover{box-shadow: 0 2px 12px 0 rgba(0,0,0,.1);border:1px solid #e0e0e0;}
  .list-box .img img{border:1px solid #e0e0e0;padding:1px;height:180px;width:100%;}
  .list-box p{text-align: center;line-height: 30px;font-size: 16px;}
  .abstract{color:#999;line-height: 24px;text-align: center;}
  .square{}
  .circle .img img{border-radius: 50%;}
</style>
