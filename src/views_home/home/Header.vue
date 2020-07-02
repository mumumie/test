<template>
  <div class="header-box">
    <div class="header-content">
      <div class="logo">{{ title }}</div>
      <div class="login" v-if="!name">
        <el-button type="text" @click="$router.push('/')">首页</el-button>
        <el-button type="text" @click="$router.push('/login')">登录</el-button>
        <el-button type="text" @click="$message.error('暂未开发！')">免费注册</el-button>
      </div>
      <div class="login" v-if="name">
        <el-button type="text" @click="$router.push('/')">欢迎您 {{ name }}</el-button>
        <el-button type="text" @click="logout">退出</el-button>
      </div>
    </div>
  </div>
</template>

<script>
import { mapGetters } from 'vuex'
export default {
  name: 'Header',
  props: {
    title: {
      type: String,
      default: ''
    }
  },
  computed: {
    // ...mapState({
    //   name: state => state.user.name
    // }),
    ...mapGetters(['name'])
  },
  created() {
  },
  methods: {
    logout() {
      this.$store.dispatch('user/logout').then(() => {
        this.$message.success('已退出登录！')
      })
    }
  }
}
</script>

<style scoped>
  .header-box{
    background: #f9f9f9;
    box-shadow: 0 2px 12px 0 rgba(0,0,0,.1);
  }
  .header-content{
    line-height: 50px;
    width: 1200px;
    margin: 0 auto;
    display: flex;
  }
  .logo{
    font-size: 20px;
    font-weight: bold;
  }
  .login{
    margin-left: auto;
    display: flex;
  }
</style>
