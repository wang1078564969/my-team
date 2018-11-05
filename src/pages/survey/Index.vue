<template>
	<div class="setting">
		<el-menu :default-active="this.currentRoute" class="el-menu-demo" mode="horizontal"  router active-text-color="#ffd04b" >
			<el-menu-item index="/survey/surveyInfo" @click="currentRoute='/survey/surveyInfo'">课调管理</el-menu-item>
			<el-menu-item index="/survey/surveyMonitor" @click="currentRoute='/survey/surveyMonitor'">课调监控</el-menu-item>
			<el-menu-item index="/survey/surveyCheck" @click="currentRoute='/survey/surveyCheck'">班级管理</el-menu-item>
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
			//将页面路由信息记录在localStorage中
			currentRoute:{
				handler: function(){
      				localStorage.setItem("surveycurrentRoute",JSON.stringify(this.currentRoute));
				},
				deep: true
			}
		},
		created(){
			 //在页面加载时读取localStorage里的状态信息
    		let currentRoute=JSON.parse(localStorage.getItem('surveycurrentRoute'));
			this.currentRoute=currentRoute
			if(this.currentRoute==null){
				this.currentRoute='/survey/surveyInfo'
				this.$router.push(this.currentRoute);
			}
		},
	}
</script>
<style>
</style>