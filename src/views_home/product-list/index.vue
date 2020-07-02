<template>
  <div class="container">
    <Header title="产品列表" />
    <div class="main">
      <div class="list-search">
        <el-form ref="form" :model="form" label-width="80px">
          <el-form-item label="活动名称">
            <el-input v-model="form.name"></el-input>
          </el-form-item>
          <el-form-item label="活动区域">
            <el-select v-model="form.region" placeholder="请选择活动区域">
              <el-option label="区域一" value="shanghai"></el-option>
              <el-option label="区域二" value="beijing"></el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="活动时间">
            <el-col :span="11">
              <el-date-picker type="date" placeholder="选择日期" v-model="form.date1" style="width: 100%;"></el-date-picker>
            </el-col>
            <el-col class="line" :span="2" style="text-align: center;">-</el-col>
            <el-col :span="11">
              <el-time-picker placeholder="选择时间" v-model="form.date2" style="width: 100%;"></el-time-picker>
            </el-col>
          </el-form-item>
          <el-form-item>
            <el-button type="primary" @click="onSubmit">搜索</el-button>
            <el-button>清空</el-button>
          </el-form-item>
        </el-form>
      </div>
      <div class="list-box">
        <div v-for="item in productList" :key="item.id" class="list-content">
          <div class="list-detail" @click="toPath(item)">
            <div>产品名称：{{item.name}}</div>
            <div>地址：{{item.location}}</div>
            <div>IP：{{item.ip}}</div>
            <div>数量：{{item.count}}</div>
          </div>
        </div>
      </div>
      <div class="pages-box">
        <el-pagination
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
          :current-page="postData.pageno + 1"
          :page-sizes="[5, 10, 20, 50, 100]"
          :page-size="postData.pagesize"
          layout="total, sizes, prev, pager, next, jumper"
          :total="pageTotal">
        </el-pagination>
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
        name: '',
        region: '',
        date: '',
        date2: ''
      },
      postData: {
        typeName: 'Device',
        pagesize: 5,
        pageno: 0
      },
      currentPage: 1,
      currentPagesize: 10,
      pageTotal: 0,
      productList: []
    }
  },
  created() {
    this.getInfo()
  },
  methods: {
    toPath(row) {
      this.$router.push({ path: 'product-detail', query: { id: row.id }})
    },
    onSubmit() {
    },
    getInfo() {
      this.$ajax.vpost('/queryBean', this.postData).then(res => {
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
  .list-search{padding:30px;border:1px solid #0a76a4;border-radius: 10px;margin-top:30px;}
  .list-box{display: flex;flex-wrap: wrap;margin-top:20px;}
  .list-content{width:25%;}
  .list-detail{padding:20px;margin:5px;border:1px solid #e0e0e0;border-radius: 4px;cursor: pointer;}
  .list-detail:hover{box-shadow: 0 2px 12px 0 rgba(0, 0, 0, .1);}
  .list-detail > div{line-height: 30px;}
  .pages-box{text-align: center;margin:20px 0;}
</style>
