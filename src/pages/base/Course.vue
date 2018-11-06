<template>
	<div class="course">
		<!-- 按钮区 -->
		<div class="btns">
			<el-button size='mini' @click='toAddcourse'>添加</el-button>
			<el-button size='mini' @click='batchDelete()'>批量删除</el-button>
			 <el-input size='mini' 
			 	v-model="keywords"
			    remote
			    reserve-keyword
			    placeholder="请输入关键词"
			    style='width: 180px;margin-left: 10px'></el-input>
		</div>
		<!-- 按钮区 -->
		<!-- 表格区 -->
		<div class="course_tbl" :loading='loading'>
			<el-table
		      :data="course"
		      size='mini'
		      border
		      style="width: 100%"
		      @selection-change="handleSelectionChange">
		      <el-table-column 
		      type="selection"
		      width="40">
		      </el-table-column>
		      <el-table-column
		        prop="id"
		        label="编号"
		        width="80">
		      </el-table-column>
		      <el-table-column
		        prop="name"
		        label="课程名称"
		        width="180">
		      </el-table-column>
		      <el-table-column
		        prop="period"
		        label="课时"
		        width="180">
		      </el-table-column>
		      <el-table-column
		        prop="descriptioin"
		        label="课程简介">
		      </el-table-column>
		      <el-table-column
		        label="操作"
		        width='120'>
		        <template slot-scope='{row}'>
			        <i class="fa fa-trash" title="删除" @click='deleteById(row.id)'></i>
		        	<i class="fa fa-pencil" title="修改" @click='toUpdata(row)'></i>
		        </template>
		      </el-table-column>
		    </el-table>
		</div>
		<!-- 表格区 -->
		<!-- 模态框 -->
		<el-dialog
		  :title="GoDialog.title"
		  :visible.sync="GoDialog.visible"
		  width="30%" >
		  <el-form label-position="left" label-width="80px" :model="GoDialog.form">
		      {{GoDialog.form}}
		  	<el-form-item label="课程名称">
			    <el-input v-model='GoDialog.form.name'></el-input>
			 </el-form-item>
			 <el-form-item label="课时">
			    <el-input v-model='GoDialog.form.period'></el-input>
			 </el-form-item>
			 <el-form-item label="课程介绍">
			    <el-input type="textarea" v-model='GoDialog.form.descriptioin'></el-input>
			 </el-form-item>
		  </el-form>
		  <span slot="footer" class="dialog-footer">
		    <el-button size='mini' @click='toClose'>取 消</el-button>
		    <el-button type="primary" size='mini' @click='saveOrUpdate'>保 存</el-button>
		  </span>
		</el-dialog>
		<!-- 模态框 -->
	</div>
</template>
<script>
import getAxios from '@/http/axios'
let axios = getAxios();
	export default {
		data(){
			return{
				loading:false,
				keywords:'',
				course:[],
				multipleSelection:[],
				GoDialog:{
					title:'',
					visible:false,
					form:{}
				}
			}
		},
		created(){
			this.findAllcourse()
		},
		methods:{
			// 修改
			toUpdata(row){
				this.GoDialog.title='修改课程信息'
				this.GoDialog.visible=true;
				this.GoDialog.form=row;
			},
			// 批量删除功能未实现
			batchDelete(){
				this.$confirm('此操作将永久删除该数据, 是否继续?', '提示', {
		          confirmButtonText: '确定',
		          cancelButtonText: '取消',
		          type: 'warning'
		        }).then(()=>{
		        	let ids = this.multipleSelection.map((item)=>{
						return item.id;
					})
					axios.post('/course/batchDelete',{ids})
					.then(()=>{
						 this.$notify({
				          title: '成功',
				          message: '删除成功',
				          type: 'success'
				        });
						 this.findAllcourse();
					})
					.catch(()=>{
						this.$notify.error({
				          title: '错误',
				          message: '删除失败'
				        });
					})
		        })
			},
			// 通过id删除
			deleteById(id){
				this.$confirm('此操作将永久删除该年级, 是否继续?', '提示', {
		          confirmButtonText: '确定',
		          cancelButtonText: '取消',
		          type: 'warning'
		        })
		        .then(()=>{
		        	axios.get('/course/deleteById?id='+id)
					.then(()=>{
						this.$notify.success({
				          title: '删除成功',
				          message: '成功'
				        });
				        this.findAllcourse();
					})
					.catch(()=>{
						this.$notify.error({
				          title: '删除异常',
				          message: '错误'
				        });
					})
		        })
			},
			// 保存或修改
			saveOrUpdate(){
				axios.post('/course/saveOrUpdate',this.GoDialog.form)
				.then(()=>{
					this.$notify.success({
			          title: '提交成功',
			          message: '成功'
			        });
			        this.findAllcourse();
					this.GoDialog.visible=false;
				})
				.catch(()=>{
					this.$notify.error({
			          title: '错误',
			          message: '错误'
			        });
				})
			},
			// 关闭模态框
			toClose(){
				this.GoDialog.visible=false;
				this.GoDialog.form={};
			},
			// 添加
			toAddcourse(){
				this.GoDialog.title='添加课程信息'
				this.GoDialog.visible=true;
				this.GoDialog.form={};
			},
			// 复选框勾选
			handleSelectionChange(val){
				this.multipleSelection = val;
			},
			// 查询功能
			findAllcourse(){
				this.loading=true
				axios.get('/course/findAllCourse')
				.then(({data:result})=>{
					this.course=result.data;
				})
				.catch(()=>{
					this.$notify.error({
			          title: '错误',
			          message: '这是一条错误的提示消息'
			        });
				})
				.finally(()=>{
					this.loading=false
				})
			}
		}
	}
</script>
<style>
	.btns{
		margin:10px 0 10px 0;
	}
	.course_tbl i.fa{
		margin-right: 15px;
		font-size: 18px;
		cursor: pointer;
	}
	.course_tbl i.fa-pencil{
		color: #67C23A;

	}
	.course_tbl i.fa-trash{
		color: #F56C6C;

	}
</style>