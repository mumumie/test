<template>
  <div id="app">
    <router-view />
    <float-window />
    <!-- you can add element-ui's tooltip -->
    <el-tooltip placement="top" content="tooltip">
      <back-to-top :custom-style="myBackToTopStyle" :visibility-height="200" :back-position="50" transition-name="fade" />
    </el-tooltip>
  </div>
</template>

<script>
export default {
  name: 'App',
  components: {
    floatWindow: () => import('@/views_home/home/floatWindow'),
    BackToTop: () => import('@/components/BackToTop')
  },
  data() {
    return {
      myBackToTopStyle: {
        right: '50px',
        bottom: '50px',
        width: '40px',
        height: '40px',
        'border-radius': '4px',
        'line-height': '45px', // 请保持与高度一致以垂直居中 Please keep consistent with height to center vertically
        background: '#e7eaf1'// 按钮的背景颜色 The background color of the button
      }
    }
  }
}
</script>
