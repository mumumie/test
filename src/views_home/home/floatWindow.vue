<template>
  <div class="float-window">
    <el-tooltip placement="left">
      <div slot="content">
        <img src="../../assets/images/wx.jpg" alt="">
      </div>
      <div><i class="el-icon-s-shop" /></div>
    </el-tooltip>

  </div>
</template>

<script>
export default {
  name: 'FloatWindow'
}
</script>

<style scoped>
  .float-window{position:fixed;top:300px;right:5px;border-radius: 3px;background: #f8f8f8;color:#1890ff;font-size: 25px;box-shadow: 0 2px 12px 0 rgba(0, 0, 0, .1)}
  .float-window > div{
    width:50px;height:50px;line-height: 50px;text-align: center;cursor: pointer;
  }
  .float-window > div:hover{background: #1890ff;color:#fff;}
</style>
