<template>
  <div class="container">
    <Header title="产品列表-叠合楼板" />
    <div class="main">
      <div class="list-search">
        <el-form ref="form" :model="form" label-width="80px" inline>
          <el-form-item label="产品类型">
            <el-select v-model="form.type" placeholder="产品类型" clearable>
              <el-option v-for="item in productTypeList" :key="item.id" :label="item.name" :value="1" />
            </el-select>
          </el-form-item>
          <el-form-item label="产品名称">
            <el-input v-model="form.name" style="width:200px;" placeholder="关键字" />
          </el-form-item>
          <el-form-item label="发布时间">
            <el-date-picker
              v-model="date"
              type="daterange"
              value-format="timestamp"
              range-separator="至"
              start-placeholder="开始日期"
              end-placeholder="结束日期"
            />
          </el-form-item>
          <el-form-item>
            <el-button type="primary" @click="onSubmit">搜索</el-button>
            <el-button @click="clearHandle">清空</el-button>
          </el-form-item>
        </el-form>
      </div>
      <div class="list-box">
        <div v-for="item in productList" :key="item.id" class="list-content">
          <div class="list-detail" @click="toPath(item)">
            <div>产品名称：{{ item.name }}</div>
            <div>产品长度a：{{ item.length }}</div>
            <div>产品宽度b：{{ item.width }}</div>
            <div>产品厚度H：{{ item.height }}</div>
            <div>a边伸筋（0，1）：{{ item.absj }}</div>
            <div>b边伸筋（0，1）：{{ item.bbsj }}</div>
          </div>
        </div>
        <div v-if="productList.length === 0" class="list-none">暂无数据！</div>
      </div>
      <div class="pages-box">
        <el-pagination
          :current-page="postData.pageno + 1"
          :page-sizes="[8, 12, 16, 32, 64]"
          :page-size="postData.pagesize"
          layout="total, sizes, prev, pager, next, jumper"
          :total="pageTotal"
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
        />
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'ProductList',
  components: {
    Header: () => import('../home/Header')
  },
  data() {
    return {
      form: {
        name: this.$route.query.keyword || '',
        date1: '',
        date2: ''
      },
      date: null,
      postData: {
        typeName: 'P_dhlb',
        pagesize: 8,
        pageno: 0
      },
      currentPage: 1,
      currentPagesize: 10,
      pageTotal: 0,
      productList: [],
      productTypeList: []
    }
  },
  watch: {
    date(val) {
      if (val && val.length === 2) {
        this.form.date1 = val[0]
        this.form.date2 = val[1] + 24 * 60 * 60 * 1000
      } else {
        this.form.date1 = ''
        this.form.date2 = ''
      }
    }
  },
  created() {
    this.getProductType()
    this.getInfo()
  },
  methods: {
    getProductType() {
      const postData = {
        typeName: 'ProductType',
        pagesize: 4,
        pageno: 0
      }
      this.$ajax.vpost('/queryBean', postData).then(res => {
        this.productTypeList = res.bean.data
      }).catch(() => {})
    },
    clearHandle() {
      this.form = {
        name: '',
        date1: '',
        date2: ''
      }
      this.date = null
      this.getInfo()
    },
    toPath(row) {
      // this.$router.push({ path: 'product-detail', query: { id: row.id, typeName: this.postData.typeName }})
      const { href } = this.$router.resolve({
        path: `product-detail`,
        query: {
          id: row.id,
          typeName: this.postData.typeName
        }
      })
      window.open(href, '_blank')
    },
    onSubmit() {
      this.postData.pageno = 0
      this.getInfo()
    },
    getInfo() {
      const params = Object.assign(this.postData, { condition: {}})
      if (this.form.name) {
        params.condition['name$regex'] = this.form.name
      }
      if (this.form.type) {
        params.condition['type'] = this.form.type
      }
      if (this.form.date1) {
        params.condition['insertDt$in'] = [this.form.date1, this.form.date2]
      }
      this.$ajax.vpost('/queryBean', params).then(res => {
        this.productList = res.bean.data
        this.pageTotal = res.bean.total
      }).catch(() => {})
    },
    handleSizeChange(val) {
      console.log(`每页 ${val} 条`)
      this.postData.pagesize = val
      this.postData.pageno = 0
      this.getInfo()
    },
    handleCurrentChange(val) {
      console.log(`当前页: ${val}`)
      this.postData.pageno = val - 1
      this.getInfo()
    }
  }
}
</script>

<style scoped>
  .main{width:1200px;margin: 0 auto;}
  .list-search{padding:30px 30px 10px;border:1px solid #0a76a4;border-radius: 10px;margin-top:30px;}
  .list-box{display: flex;flex-wrap: wrap;margin-top:20px;}
  .list-none{text-align: center;font-size:20px;line-height:200px;width:100%;background: #f9f9f9;}
  .list-content{width:25%;}
  .list-detail{padding:20px;margin:5px;border:1px solid #e0e0e0;border-radius: 4px;cursor: pointer;}
  .list-detail:hover{box-shadow: 0 2px 12px 0 rgba(0, 0, 0, .1);}
  .list-detail > div{line-height: 30px;}
  .pages-box{text-align: center;margin:20px 0;}
</style>
