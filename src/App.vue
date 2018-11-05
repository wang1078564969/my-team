<template>
  <div class="app">
    <!-- 头 -->
    <div class="header">
      <div class="title">
        <i class="fa fa-tv"></i> &nbsp;&nbsp;
        智慧校园-评教系统
      </div>
      <div class="info">
        <img class="photo" :src="user.userface" alt="">
        <div class="u">
          <el-dropdown @command='handleCommand' size='mini'>
            <span class="el-dropdown-link">
              {{user.nickname}}<i class="el-icon-arrow-down el-icon--right"></i>
            </span>
            <el-dropdown-menu slot="dropdown">
              <el-dropdown-item>个人中心</el-dropdown-item>
              <el-dropdown-item command='logout'>退出</el-dropdown-item>
            </el-dropdown-menu>
          </el-dropdown>
        </div>
        
      </div>
    </div>
    <!-- 体 -->
    <div class="center">
      <!-- 左侧导航 -->
      <div class="left-nav">
        <div class="i" @click="open()"><i :class="this.class"></i></div>
        <el-menu :default-active="this.currentRoute" class="el-menu-vertical-demo" @open="handleOpen" @close="handleClose" :collapse="Collapse" router>
          <el-menu-item index="/">
            <i class="el-icon-document"></i>
            <span slot="title">首页</span>
          </el-menu-item>
          <el-menu-item index="/base">
            <i class="el-icon-location"></i>
            <span slot="title">基础数据</span>
          </el-menu-item>
          <el-menu-item index="/qn">
            <i class="el-icon-location"></i>
            <span slot="title">问卷管理</span>
          </el-menu-item>
          <el-menu-item index="/survey">
            <i class="el-icon-location"></i>
            <span slot="title">课调管理</span>
          </el-menu-item>
          <el-menu-item index="/statistics">
            <i class="el-icon-location"></i>
            <span slot="title">课调统计</span>
          </el-menu-item>
        </el-menu>
      </div>
      <!-- 右侧内容区 -->
      <div class="content">
        <div class="wrapper">
          <router-view></router-view>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
  import axios from '@/http/axios'
  export default {
    data(){
      return {
        class:'el-icon-d-arrow-right',
        Collapse:true,
        currentRoute:{},
        user:{}
      }
    },
    watch:{
      //将页面路由信息记录在localStorage中
			currentRoute:{
				handler: function(){
      				localStorage.setItem("currentRoute",JSON.stringify(this.currentRoute));
				},
				deep: true
			}
    },
    created(){
      this.currentRoute = this.$route.path;
      // 从localStorage中获取用户信息，用于在页面中显示
      /*
      let user = JSON.parse(localStorage.getItem('user'));
      if(user && user.id){
        this.user = user;
      } else {
        window.vm.currentComponent = 'Login';
      }
      */
      //在页面加载时读取localStorage里的状态信息
    	let currentRoute=JSON.parse(localStorage.getItem('currentRoute'));
			this.currentRoute=currentRoute
			if(this.currentRoute==null){
				this.currentRoute='/'
				this.$router.push(this.currentRoute);
			}
    },
    methods:{
      handleCommand(command){
        if(command == 'logout'){
          axios.get('/logout').then(()=>{
            //跳转
            window.vm.currentComponent = 'Login';
            //清理localstorage中的user
            localStorage.removeItem('user');
          });
        }
      },
      handleOpen(key, keyPath) {
        console.log(key, keyPath);
      },
      handleClose(key, keyPath) {
        console.log(key, keyPath);
      },
      open(){
        if(this.Collapse==true){
          this.class='el-icon-d-arrow-left'
          this.Collapse=false
        }else{
          this.Collapse=true
          this.class='el-icon-d-arrow-right'
        }
      }
    }
  }
</script>
<style>
.el-menu-vertical-demo:not(.el-menu--collapse) {
    width: 200px;
    min-height: 400px;
    
  }
  html {
    font: normal normal 12px '微软雅黑','Microsoft YaHei';
    color: #666
  }
  input:-webkit-autofill {
    -webkit-box-shadow: 0 0 0px 1000px white inset !important;
  }
  body , ul ,ol,dl ,p, h1,h2,h3 {
    margin: 0;
    padding: 0;
    background-color:#fff;
  }
  ul , ol {
    list-style: none;
  }
  a {
    color: #666;
    text-decoration: none;
  }
  div {
    box-sizing: border-box;
  }
  .el-dialog__body {
    padding: 2em 2em 0 2em;
  }

  .header {
    display: flex;
    position: absolute;
    width: 100%;
    height: 60px;
    top: 0;
    background-color: teal;   
    padding: 0 1em; 
  }
  .header .title {
    color: #ffffff;
    line-height: 60px;
    font-size: 18px;
    float: left;
  }
  .header .photo {
    width: 50px;
    height: 50px;
    border-radius: 50%;
    margin-top: 5px;
    margin-right: 1em;
  }
  .header .info {
    position:absolute;
    right: 50px;
    cursor: pointer;
  }
  .header .info .u {
    float: right;
    line-height: 20px;
    margin-top: 20px;
  }
  .header .info .el-dropdown {
    color: #fff;
  }
  .center {
    position: absolute;
    top: 60px;
    bottom: 0;
    width: 100%;
  }
  .center > .left-nav {
    height: 100%;
    float: left;
  }
  .center .i>i{
    display: block;
    margin: 5px 5px;
    text-align: right
  }
  .center > .left-nav > ul > li.current {
    background-color: #f0f0f0;
  }
  .center > .content {
    height: 100%;
    background-color: #f0f0f0;
    padding: 1em 1em 0 1em;
    overflow-y: auto;
  }
  .center > .content > .wrapper {
    width: 100%;
    height: 100%;
    background-color: #ffffff;
    border-radius: 5px;
    padding: .5em;
    overflow-y: auto;
  }
</style>