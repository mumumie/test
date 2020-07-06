<template>
  <div class="container">
    <Header title="产品详情" />
    <div class="main">
      <div class="title">
        {{ productInfo.name }}
      </div>
      <div class="content">
        <div v-for="item in keyInfo" :key="item.name" class="list">
          <div>{{ item.zhName }}</div>
          <div v-if="item.zhName === '附件'">
            <p v-for="(file, i) in productInfo[item.name]" :key="i">
              <a :href="file" target="_blank"><i class="el-icon-folder" />{{ filterFile(file) }}</a>
            </p>
          </div>
          <div v-else-if="item.fieldType === 'RADIO'">
            {{ item.verifyDesc[productInfo[item.name]] }}
          </div>
          <div v-else-if="item.fieldType === 'DATE'">
            {{ productInfo[item.name] | formatDate('yyyy-MM-dd hh : mm') }}
          </div>
          <div v-else>
            {{ productInfo[item.name] + (item.unit || '') }}
          </div>
        </div>
      </div>
      <!--      <table class="content" v-if="keyInfo.length > 0">-->
      <!--        <tr v-for="(item, index) in keyInfo" :key="index">-->
      <!--          <template v-for="obj in item">-->
      <!--            <td class="bg" :key="obj.name">{{obj.zhName}}</td>-->
      <!--            <td :key="obj.name">{{ productInfo[obj.name] }}</td>-->
      <!--          </template>-->
      <!--        </tr>-->
      <!--      </table>-->
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
      keyInfo: []
    }
  },
  created() {
    this.getProduct()
    this.getTableInfo()
  },
  methods: {
    filterFile(val) {
      if (val) {
        const arr = val.split('/')
        return arr[arr.length - 1]
      } else {
        return '-'
      }
    },
    getTableInfo() {
      const params = {
        typeName: this.$route.query.typeName
      }
      return new Promise((resolve, reject) => {
        this.$ajax.vpost('getTableInfo', params).then(res => {
          // console.log(res.bean.fieldProps)
          // res.bean.fieldProps.forEach((v, i) => {
          //   console.log(i / 2)
          //   console.log(i % 2)
          //   if (i % 2 === 0) {
          //     this.$set(this.keyInfo, i / 2, [])
          //   }
          //   this.keyInfo[parseInt(i / 2)][(i % 2)] = v
          // })
          // console.log(this.keyInfo)
          this.keyInfo = res.bean.fieldProps
          resolve()
        }).catch(err => {
          reject(err)
        })
      })
    },
    getProduct() {
      const params = {
        typeName: this.$route.query.typeName,
        id: this.$route.query.id
      }
      this.$ajax.vpost('getBean', params).then(res => {
        this.productInfo = res.bean
      })
    }
  }
}
</script>

<style scoped>
  .main{width:1200px;margin: 30px auto;}
  .title{line-height: 50px;font-size: 23px;font-weight: bold;text-align: center;}
  .content{width:100%;border-top:1px solid  #eee;border-left:1px solid #eee;display: flex;flex-wrap: wrap;margin-top:20px;}
  .content .list{width:50%;display: flex;line-height: 40px;}
  .content .list div{border-right:1px solid #e0e0e0;border-bottom:1px solid #e0e0e0;}
  .content .list > div:first-child{width:200px;flex: none;background: #f8f8f8;padding:0 20px;}
  .content .list > div:last-child{width:400px;flex: auto;padding:0 20px;box-sizing: border-box;}
  .content a{text-decoration: underline;color:#1890ff;}
  .content a:hover{color:#f00;}
</style>
