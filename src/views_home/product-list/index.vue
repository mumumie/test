<template>
  <div class="container">
    <Header />
    <Banner :class-active="3" />
    <div class="main">
      <div class="nav-left">
        <div class="nav-title">产品分类</div>
        <div class="nav-content">
          <div
            v-for="item in productTypeList"
            :key="item.id"
            class="nav-list"
            :class="{ active: item.className === postData.typeName }"
            @click="typeNameChange(item)"
          >
            {{ item.name }}
          </div>
        </div>
      </div>
      <div class="product-list">
        <div class="search-box">
          <div class="search-input">
            <span>产品名称</span>
            <el-input v-model="form.name" size="mini" style="width:200px;" placeholder="关键字" />
            <span>生产地址</span>
            <el-input v-model="form.productAddress" size="mini" style="width:200px;" placeholder="关键字" />
            <el-button type="primary" size="mini" style="margin-left:20px;" @click="onSubmit">搜索</el-button>
            <el-button type="primary" size="mini" style="margin-left:20px;" @click="exportFile">导出</el-button>
            <el-button type="primary" size="mini" style="margin-left:20px;" @click="importFile">导入</el-button>
          </div>
          <div v-for="item in searchData" :key="item.value" class="search-filter">
            <div class="title">{{ item.title }}</div>
            <div v-for="v in item.list" :key="v" class="condition"><span :class="{active: v === form[item.value]}" @click="searchHandle(item, v)">{{ v }}</span></div>
            <div class="input">
              <el-input v-model="item.input[0]" size="mini" style="width:80px;" />
              -
              <el-input v-model="item.input[1]" size="mini" style="width:80px;" />
              <el-button type="primary" size="mini" style="margin-left:10px;" @click="searchHandle(item, Arrayfilter([item.input[0], item.input[1]]))">确定</el-button>
            </div>
          </div>
          <div class="search-condition">
            <div class="title">搜索条件 >></div>
            <div class="condition">
              <div v-for="item in searchCondition" :key="item.label" class="msgbox">
                <span>{{ valueName[item.label] }}: </span>
                <span style="color:#f00;">{{ item.value }}</span>
                <i class="el-icon-close" @click="deleteSearchCondition(item.label)" />
              </div>
            </div>
            <div class="btn">
              <el-button type="danger" size="mini" icon="el-icon-delete" @click="deleteSearchCondition('all')">清空搜索选项</el-button>
            </div>
          </div>
        </div>
        <div class="list-box">
          <div v-for="item in productList" :key="item.id" class="list-content">
            <div class="list-detail" @click="toPath(item)">
              <div>产品名称：{{ item.name }}</div>
              <div v-for="{ value } in searchData" :key="value">{{ valueName[value] + ':' + item[value] }}</div>
              <!--              <div>产品长度a：{{ item.length }}</div>-->
              <!--              <div>产品宽度b：{{ item.width }}</div>-->
              <!--              <div>产品厚度H：{{ item.height }}</div>-->
              <!--              <div>a边伸筋（0，1）：{{ item.absj }}</div>-->
              <!--              <div>b边伸筋（0，1）：{{ item.bbsj }}</div>-->
              <!--              <div>生产地址：{{ item.productAddress }}</div>-->
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
    <upload-file
      :type-name="postData.typeName"
      :switch-btn="switchBtn"
      @close="switchBtn = false"
      @success="importSuccess"
    />
  </div>
</template>

<script>
export default {
  name: 'ProductList',
  components: {
    Header: () => import('../home/Header'),
    Banner: () => import('../home/Banner'),
    uploadFile: () => import('./uploadFile')
  },
  data() {
    return {
      form: {
        name: this.$route.query.name || '',
        productAddress: this.$route.query.productAddress || '',
        length: this.$route.query.length || '',
        width: this.$route.query.width || '',
        height: this.$route.query.height || '',
        ak_3: this.$route.query.ak_3 || '',
        scj_1: this.$route.query.scj_1 || '',
        scj_2: this.$route.query.scj_2 || '',
        bhc_rh1: this.$route.query.bhc_rh1 || '',
        a_bsj_rd1: this.$route.query.a_bsj_rd1 || '',
        a_bsj_rs1: this.$route.query.a_bsj_rs1 || '',
        b_bsj_rd2: this.$route.query.b_bsj_rd2 || '',
        b_bsj_rs1: this.$route.query.b_bsj_rs1 || ''
      },
      date: null,
      postData: {
        typeName: this.$route.query.type,
        pagesize: 8,
        pageno: 0
      },
      pageTotal: 0,
      productList: [],
      productTypeList: [],
      typeName: '',
      searchData: [
        { title: '长度a(毫米)', list: ['≤8', '8-15', '15-20', '20-30', '≥30'], value: 'length', input: [null, null] },
        { title: '宽度b(毫米)', list: ['≤8', '8-15', '15-20', '20-30', '≥30'], value: 'width', input: [null, null] },
        { title: '厚度H(毫米)', list: ['≤8', '8-15', '15-20', '20-30', '≥30'], value: 'height', input: [null, null] },
        { title: '凹口深度h1', list: ['≤8', '8-15', '15-20', '20-30', '≥30'], value: 'ak_3', input: [null, null] },
        { title: '伸出筋直径rd1', list: ['≤8', '8-15', '15-20', '20-30', '≥30'], value: 'scj_1', input: [null, null] },
        { title: '伸出筋根数rm', list: ['≤8', '8-15', '15-20', '20-30', '≥30'], value: 'scj_2', input: [null, null] }
      ],
      typeSearchData: {
        P_dhkjl: [
          { title: '长度a(毫米)', list: ['≤8', '8-15', '15-20', '20-30', '≥30'], value: 'length', input: [null, null] },
          { title: '宽度b(毫米)', list: ['≤8', '8-15', '15-20', '20-30', '≥30'], value: 'width', input: [null, null] },
          { title: '厚度H(毫米)', list: ['≤8', '8-15', '15-20', '20-30', '≥30'], value: 'height', input: [null, null] },
          { title: '凹口深度h1', list: ['≤8', '8-15', '15-20', '20-30', '≥30'], value: 'ak_3', input: [null, null] },
          { title: '伸出筋直径rd1', list: ['≤8', '8-15', '15-20', '20-30', '≥30'], value: 'scj_1', input: [null, null] },
          { title: '伸出筋根数rm', list: ['≤8', '8-15', '15-20', '20-30', '≥30'], value: 'scj_2', input: [null, null] }
        ],
        P_dhlb: [
          { title: '长度a(毫米)', list: ['≤8', '8-15', '15-20', '20-30', '≥30'], value: 'length', input: [null, null] },
          { title: '宽度b(毫米)', list: ['≤8', '8-15', '15-20', '20-30', '≥30'], value: 'width', input: [null, null] },
          { title: '厚度H(毫米)', list: ['≤8', '8-15', '15-20', '20-30', '≥30'], value: 'height', input: [null, null] },
          { title: '保护层厚度rh1', list: ['≤8', '8-15', '15-20', '20-30', '≥30'], value: 'bhc_rh1', input: [null, null] },
          { title: 'a边伸筋直径rd1', list: ['≤8', '8-15', '15-20', '20-30', '≥30'], value: 'a_bsj_rd1', input: [null, null] },
          { title: 'a边伸筋间距rs1', list: ['≤8', '8-15', '15-20', '20-30', '≥30'], value: 'a_bsj_rs1', input: [null, null] },
          { title: 'b边伸筋直径rd2', list: ['≤8', '8-15', '15-20', '20-30', '≥30'], value: 'b_bsj_rd2', input: [null, null] },
          { title: 'b边伸筋间距rs1', list: ['≤8', '8-15', '15-20', '20-30', '≥30'], value: 'b_bsj_rs1', input: [null, null] }
        ]
      },
      searchCondition: [],
      valueName: {
        length: '长度a',
        width: '宽度b',
        height: '厚度H',
        name: '产品名称',
        productAddress: '生产地址',
        ak_3: '凹口深度h1',
        scj_1: '伸出筋直径rd1',
        scj_2: '伸出筋根数rm',
        bhc_rh1: '保护层厚度rh1',
        a_bsj_rd1: 'a边伸筋直径rd1',
        a_bsj_rs1: 'a边伸筋间距rs1',
        b_bsj_rd2: 'b边伸筋直径rd2',
        b_bsj_rs1: 'b边伸筋间距rs1'
      },
      switchBtn: false
    }
  },
  watch: {
    // date(val) {
    //   if (val && val.length === 2) {
    //     this.form.date1 = val[0]
    //     this.form.date2 = val[1] + 24 * 60 * 60 * 1000
    //   } else {
    //     this.form.date1 = ''
    //     this.form.date2 = ''
    //   }
    // },
    productTypeList: {
      handler(val) {
        if (val.length > 0) {
          if (this.postData.typeName) {
            const row = val.find(v => v.className === this.postData.typeName)
            this.searchData = this.typeSearchData[row.className]
            this.typeName = row.name
          } else {
            this.typeName = val[0].name
            this.postData.typeName = val[0].className
            this.searchData = this.typeSearchData[val[0].className]
          }
        }
      },
      immediate: true
    }
  },
  created() {
    this.getProductType().then(() => {
      this.initSearchData()
      this.getInfo()
    })
  },
  methods: {
    importSuccess() {
      this.$message.success('导入成功！')
      this.switchBtn = false
    },
    // 导入文件
    importFile() {
      this.switchBtn = true
    },
    // 导出文件
    exportFile() {
      window.open(`http://139.224.234.131:8088/getExcelTemp?typeName=${this.postData.typeName}`, '_blank')
    },
    initSearchData() {
      const query = this.$route.query
      for (const key in query) {
        if (key !== 'type') {
          this.searchCondition.push({ label: key, value: query[key] })
        }
        const index = this.searchData.findIndex(v => v.value === key)
        if (index > -1) {
          if (query[key].indexOf('-') > -1) {
            const arr = query[key].split('-')
            this.searchData[index].input = arr
          } else if (this.form[key].indexOf('≥') > -1) {
            this.searchData[index].input = [query[key].replace(/[^0-9]/ig, ''), null]
          } else if (this.form[key].indexOf('≤') > -1) {
            this.searchData[index].input = [0, query[key].replace(/[^0-9]/ig, ''), null]
          }
        }
      }
    },
    deleteSearchCondition(val) {
      if (val === 'all') {
        Object.keys(this.valueName).forEach(key => {
          this.form[key] = null
        })
        this.searchCondition = []
        this.searchData.map(v => {
          v.input = [null, null]
        })
      } else if (val === 'name' || val === 'productAddress') {
        this.form[val] = ''
        this.searchCondition.splice(this.searchCondition.findIndex(v => v.label === val), 1)
      } else {
        this.form[val] = ''
        this.searchCondition.splice(this.searchCondition.findIndex(v => v.label === val), 1)
        this.searchData.find(v => v.value === val).input = [null, null]
      }
      this.getInfo()
    },
    Arrayfilter(arr) {
      if (!arr[0] && !arr[1]) {
        return ''
      } else if (!arr[0]) {
        return `≤${arr[1]}`
      } else if (!arr[1]) {
        return `≥${arr[0]}`
      } else {
        return `${arr[0]}-${arr[1]}`
      }
    },
    searchHandle(row, val) {
      if (val.indexOf('-') > -1) {
        row.input = val.split('-')
        if (isNaN(row.input[0]) || isNaN(row.input[0])) {
          this.$message.error('请输入数字！')
          return
        }
      } else if (val.indexOf('≥') > -1) {
        row.input = [val.replace(/≥/ig, ''), null]
        if (isNaN(row.input[0])) {
          this.$message.error('请输入数字！')
          return
        }
      } else if (val.indexOf('≤') > -1) {
        row.input = [0, val.replace(/≤/ig, '')]
        if (isNaN(row.input[1])) {
          this.$message.error('请输入数字！')
          return
        }
      }
      this.form[row.value] = val
      const index = this.searchCondition.findIndex(v => v.label === row.value)
      if (index > -1) {
        this.searchCondition.splice(index, 1)
      }
      if (val) {
        this.searchCondition.push({ label: row.value, value: val })
      }
      this.getInfo()
    },
    typeNameChange(row) {
      this.postData.typeName = row.className
      this.typeName = row.name
      Object.keys(this.valueName).forEach(key => {
        this.form[key] = null
      })
      this.searchCondition = []
      this.searchData = this.typeSearchData[row.className]
      this.postData.pageno = 0
      this.getInfo()
    },
    getProductType() {
      const postData = {
        typeName: 'ProductType',
        pagesize: 10,
        pageno: 0
      }
      return new Promise((resolve, reject) => {
        this.$ajax.vpost('/queryBean', postData).then(res => {
          this.productTypeList = res.bean.data
          resolve()
        }).catch((err) => {
          reject(err)
        })
      })
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
      ;['name', 'productAddress'].forEach(v => {
        const index = this.searchCondition.findIndex(row => row.label === v)
        if (index > -1) {
          this.searchCondition.splice(index, 1)
        }
        if (this.form[v]) {
          this.searchCondition.push({ label: v, value: this.form[v] })
        }
      })
      this.getInfo()
    },
    getInfo() {
      const params = Object.assign(this.postData, { condition: {}})
      const query = JSON.parse(JSON.stringify(this.$route.query))
      query.type = params.typeName
      for (const key in this.form) {
        if (this.form[key]) {
          if (key === 'name' || key === 'productAddress') {
            params.condition[`${key}$regex`] = this.form[key]
          } else {
            if (this.form[key].indexOf('-') > -1) {
              const arr = this.form[key].split('-')
              params.condition[`${key}$gte`] = arr[0] * 1
              params.condition[`${key}$lte`] = arr[1] * 1
            } else if (this.form[key].indexOf('≥') > -1) {
              params.condition[`${key}$gte`] = this.form[key].replace(/[^0-9]/ig, '') * 1
            } else if (this.form[key].indexOf('≤') > -1) {
              params.condition[`${key}$gte`] = 0
              params.condition[`${key}$lte`] = this.form[key].replace(/[^0-9]/ig, '') * 1
            }
          }
          query[key] = this.form[key]
        } else {
          delete query[key]
        }
      }
      // if (this.form.name) {
      //   params.condition['name$regex'] = this.form.name
      //   query.name = this.form.name
      // } else {
      //   delete query.name
      // }
      // if (this.form.productAddress) {
      //   params.condition['productAddress$regex'] = this.form.productAddress
      //   query.productAddress = this.form.productAddress
      // } else {
      //   delete query.productAddress
      // }
      // if (this.form.length) {
      //   if (this.form.length.indexOf('-') > -1) {
      //     const length = this.form.length.split('-')
      //     params.condition['length$gte'] = length[0]
      //     params.condition['length$lte'] = length[1]
      //   } else if (this.form.length.indexOf('≥') > -1) {
      //     params.condition['length$gte'] = this.form.length.replace(/[^0-9]/ig, '')
      //   } else if (this.form.length.indexOf('≤') > -1) {
      //     params.condition['length$gte'] = 0
      //     params.condition['length$lte'] = this.form.length.replace(/[^0-9]/ig, '')
      //   }
      //   query.length = this.form.length
      // } else {
      //   delete query.length
      // }
      // if (this.form.width) {
      //   const length = this.form.width.split('-')
      //   params.condition['width$gte'] = length[1]
      //   params.condition['width$lte'] = length[0]
      //   query.width = this.form.width
      // } else {
      //   delete query.width
      // }
      // if (this.form.height) {
      //   const length = this.form.height.split('-')
      //   params.condition['height$gte'] = length[1]
      //   params.condition['height$lte'] = length[0]
      //   query.height = this.form.height
      // } else {
      //   delete query.height
      // }
      console.log(query)
      this.$router.push({ path: '/product-list', query: query })
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
  .main{width:1200px;margin: 0 auto;display: flex;}
  .nav-left{width:220px;flex:none;margin-right:20px;}
  .nav-title{height:45px;line-height: 45px;padding:0 10px;background: #1890ff;color:#fff;font-size: 16px;font-weight: bold;margin-top:30px;}
  .nav-content{border: 1px solid #e0e0e0;}
  .nav-list{line-height: 40px;padding:0 15px;border-bottom:1px dashed #e0e0e0;cursor: pointer;}
  .nav-list.active{background: #f9f9f9;position: relative;}
  .nav-list.active:before{content: '';display: block;width:3px;height:40px;position:absolute;top:0;left:0;background: #1890ff;}
  .product-list{flex:auto;}
  .search-box{border:1px solid #e0e0e0;border-radius: 2px;margin-top:30px;}
  .search-input{padding:0 20px;line-height: 40px;}
  .search-input span{padding:0 10px;}
  .search-filter{display: flex;height:40px;align-items: center;border-top:1px solid #e0e0e0;}
  .search-filter .title{ width:120px;flex:none;padding-left:10px;background: #f8f8f8;line-height: 39px;border-right:1px solid #e0e0e0;}
  .search-filter .condition{ width:105px;flex:none;margin-left:10px;}
  .search-filter .condition span{display: inline-block;padding:0 10px; line-height: 26px;cursor: pointer;border:1px solid transparent;}
  .search-filter .condition span:hover,.search-filter .condition span.active{border:1px solid #FF6C00;color:#FF6C00;}
  .search-filter .input{ margin-left: auto;margin-right:20px;}
  .search-condition{display: flex;border-top:1px solid #e0e0e0;align-items: flex-start;line-height: 40px;}
  .search-condition .title{width:120px;padding-left:20px;flex:none;}
  .search-condition .condition{display: flex;align-items: center;flex-wrap: wrap;padding-bottom: 7px;}
  .search-condition .condition .msgbox{padding:0 26px 0 10px;border:1px solid #e0e0e0;line-height: 26px;margin-top:7px;position:relative;margin-right:10px;cursor: pointer;}
  .search-condition .condition .msgbox:hover{border-color:#f00;}
  .search-condition .condition .msgbox i{position:absolute;cursor: pointer;top:6px;right:6px;}
  .search-condition .btn{margin-left:auto;width:140px;flex: none;}
  .list-box{display: flex;flex-wrap: wrap;margin-top:20px;}
  .list-none{text-align: center;font-size:20px;line-height:200px;width:100%;background: #f9f9f9;}
  .list-content{width:25%;}
  .list-detail{padding:20px;margin:5px;border:1px solid #e0e0e0;border-radius: 4px;cursor: pointer;}
  .list-detail:hover{box-shadow: 0 2px 12px 0 rgba(0, 0, 0, .1);}
  .list-detail > div{line-height: 30px;overflow: hidden;text-overflow: ellipsis;white-space: nowrap;}
  .pages-box{text-align: center;margin:20px 0;}
</style>
