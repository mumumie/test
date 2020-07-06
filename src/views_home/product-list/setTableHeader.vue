<template>
  <el-dialog
    title="设置表头"
    :visible.sync="switchBtn"
    width="500px"
    :close-on-click-modal="false"
    :before-close="handleClose"
  >
    <div>
      <el-checkbox :indeterminate="isIndeterminate" v-model="checkAll" @change="handleCheckAllChange">全选</el-checkbox>
      <div style="margin: 15px 0;"></div>
      <el-checkbox-group v-model="list" @change="handleCheckedCitiesChange">
        <el-checkbox v-for="key in headerList" :label="key.name" :key="key.name" style="margin-bottom:5px;">{{key.zhName}}</el-checkbox>
      </el-checkbox-group>
    </div>
    <span slot="footer" class="dialog-footer">
      <el-button @click="handleClose">取 消</el-button>
      <el-button type="primary" @click="submitSet">确 定</el-button>
    </span>
  </el-dialog>
</template>

<script>
export default {
  name: 'SetTableHeader',
  props: ['switchBtn', 'keyInfo'],
  data() {
    return {
      dialogVisible: false,
      headerList: [],
      list: [],
      checkAll: false,
      isIndeterminate: true
    }
  },
  watch: {
    switchBtn: {
      handler(val) {
        this.headerList = JSON.parse(JSON.stringify(this.keyInfo))
        this.list = this.keyInfo.filter(v => v.show).map(vk => vk.name)
        this.checkAll = this.list.length === this.headerList.length
      },
      immediate: true
    }
  },
  methods: {
    handleCheckAllChange(val) {
      const headerList = this.headerList.map(v => v.name)
      this.list = val ? headerList : []
      this.isIndeterminate = false
    },
    handleCheckedCitiesChange(value) {
      const checkedCount = value.length
      this.checkAll = checkedCount === this.headerList.length
      this.isIndeterminate = checkedCount > 0 && checkedCount < this.headerList.length
    },
    handleClose(done) {
      this.$emit('close')
    },
    submitSet() {
      this.$emit('success', this.list)
    }
  }
}
</script>

<style scoped>

</style>
