<template>
	<div class="user">
		<div class="button">
			<div class="left-button">
				<!-- <el-input placeholder="请输入内容" v-model="l" class="input-with-select">
					<el-select v-model="gradeId" slot="prepend" placeholder="请选择" width="120">
						<el-option v-for="g in grade" :label="g.name" :value="g.id" :key='g.id'></el-option>
					</el-select>
					<el-button slot="append" icon="el-icon-search"></el-button>
				</el-input> -->
			</div>
			<div class="right-button">
				<el-button type="primary" icon="el-icon-edit" circle></el-button>
				<el-button type="danger" icon="el-icon-delete" circle></el-button>
			</div>
		</div>
		<div class="table">
			<el-table
				ref="multipleTable"
				:data="classdata"
				@selection-change="handleSelectionChange">
				<el-table-column
				type="selection"
				width="55">
				</el-table-column>
				<el-table-column
				label="#"
				width="120"
				prop="id">
				</el-table-column>
				<el-table-column
				label="年级"
				width="120"
				prop="grade.name">
				</el-table-column>
				<el-table-column
				label="班主任"
				width="120"
				prop="charge.name">
				</el-table-column>
				<el-table-column
				prop="name"
				label="班级名称"
				width="120">
				</el-table-column>
				<el-table-column
				prop="description"
				label="班级介绍 "
				width="140">
				</el-table-column>
				<el-table-column
				label="操作"
				width="140">
				<template slot-scope="scope">
					<el-button type="text">移除</el-button>
					<el-button type="text">添加</el-button>
				</template>
				</el-table-column>
			</el-table>
		</div>
	</div>
</template>
<script>
  import axios from '@/http/axios'
	export default {
		data(){
			return{
				classdata:[],
				grade:[],
				gradId:{}
			}
		},
		methods:{
			 handleSelectionChange(val) {
				this.multipleSelection = val;
			},
			//获取数据
			getclassdata(){
				axios.get('/clazz/findAllVM')
				.then((result) => {
					this.classdata=result.data.data;
				}).catch((err) => {
					this.$notify.error({
	          			title: '错误',
	          			message: '网络异常！'
	        		});
				});
			},
			//年纪
			getgradedata(){
				axios.get('/grade/findAll')
				.then((result) => {
					this.grade=result.data.data;
				}).catch((err) => {
					this.$notify.error({
	          			title: '错误',
	          			message: '网络异常！'
	        		});
				});
			}
		},
		created(){
			this.getclassdata();
			this.getgradedata()
		}
	}
</script>
<style>
	 .el-select .el-input {
    width: 130px;
  }
  .button{
	  margin:10px;
  }
  .left-button{
	  float: left;
	  width: 500px
  }
  .right-button{
	  float: right;
	  margin-right: 30px;
  }
</style>