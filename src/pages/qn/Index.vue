<template>
	<div class="setting">
		<el-menu :default-active="this.currentRoute" class="el-menu-demo" mode="horizontal"  router active-text-color="#ffd04b">
			<el-menu-item index="/qn/question" @click="currentRoute='/qn/question'">题库管理</el-menu-item>
			<el-menu-item index="/qn/questionNaire" @click="currentRoute='/qn/questionNaire'">问卷管理</el-menu-item>
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
      				localStorage.setItem("qncurrentRoute",JSON.stringify(this.currentRoute));
				},
				deep: true
			}
		},
		created(){
			 //在页面加载时读取localStorage里的状态信息
    		let currentRoute=JSON.parse(localStorage.getItem('qncurrentRoute'));
			this.currentRoute=currentRoute
			if(this.currentRoute==null){
				this.currentRoute='/qn/question'
				this.$router.push(this.currentRoute);
			}
		},
	}
</script>
<style>

</style>