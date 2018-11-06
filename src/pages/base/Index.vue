<template>
	<div class="setting">
		<el-menu :default-active="this.currentRoute" class="el-menu-demo" mode="horizontal"  router  >
			<el-menu-item index="/base/school" @click="currentRoute='/base/school'">校园信息</el-menu-item>
			<el-menu-item index="/base/grade" @click="currentRoute='/base/grade'">年级管理</el-menu-item>
			<el-menu-item index="/base/clazz" @click="currentRoute='/base/clazz'">班级管理</el-menu-item>
			<el-menu-item index="/base/course" @click="currentRoute='/base/course'">课程管理</el-menu-item>
			<el-menu-item index="/base/user" @click="currentRoute='/base/user'">用户管理</el-menu-item>
		</el-menu>
		<div class="setting-content">
			<router-view></router-view>
		</div>
	</div>
</template>
<script>
	export default {
		data(){
			return {
				currentRoute:{}
			}
		},
		watch:{
			//将页面路由信息记录在sessionStorag中
			currentRoute:{
				handler: function(){
      				sessionStorage.setItem("basecurrentRoute",JSON.stringify(this.currentRoute));
				},
				deep: true
			}
		},
		created(){
			 //在页面加载时读取sessionStorag里的状态信息
    		let currentRoute=JSON.parse(sessionStorage.getItem('basecurrentRoute'));
			this.currentRoute=currentRoute
			if(this.currentRoute==null){
				this.currentRoute='/base/school'
				this.$router.push(this.currentRoute);
			}
		},
	}
</script>
<style>

</style>