<template>
	<div class="grade">
		<!-- 按钮区 -->
		<div class="btns">
			<el-button size='mini' @click='toAddgrade'>添加</el-button>
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
		<div class="grade_tbl" :loading='loading'>
			<el-table
		      :data="grades"
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
		        label="名称"
		        width="180">
		      </el-table-column>
		      <el-table-column
		        prop="descriptioin"
		        label="简介">
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
		  :title="GDialog.title"
		  :visible.sync="GDialog.visible"
		  width="30%" >
		  <el-form label-position="left" label-width="80px" :model="GDialog.form">
		      {{GDialog.form}}
		  	<el-form-item label="年级名称">
			    <el-input v-model='GDialog.form.name'></el-input>
			 </el-form-item>
			 <el-form-item label="年级介绍">
			    <el-input type="textarea" v-model='GDialog.form.descriptioin'></el-input>
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
				grades:[],
				multipleSelection:[],
				GDialog:{
					title:'',
					visible:false,
					form:{}
				}
			}
		},
		created(){
			this.findAllgrade()
		},
		methods:{
			// 修改
			toUpdata(row){
				this.GDialog.title='修改年级信息'
				this.GDialog.visible=true;
				this.GDialog.form=row;
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
					axios.get('/grade/batchDelete',{ids})
					.then(()=>{
						 this.$notify({
				          title: '成功',
				          message: '删除成功',
				          type: 'success'
				        });
						 this.findAllgrade();
					})
					.catch(()=>{
						this.$notify.error({
				          title: '错误',
				          message: '删除失败'
				        });
					})
		        })
			},
			handleSelectionChange(val){
				this.multipleSelection = val;
			},
			// 根据id删除年级信息
			deleteById(id){
				this.$confirm('此操作将永久删除该年级, 是否继续?', '提示', {
		          confirmButtonText: '确定',
		          cancelButtonText: '取消',
		          type: 'warning'
		        })
		        .then(()=>{
		        	axios.get('/grade/deleteById?id='+id)
					.then(()=>{
						this.$notify.success({
				          title: '删除成功',
				          message: '成功'
				        });
				        this.findAllgrade();
					})
					.catch(()=>{
						this.$notify.error({
				          title: '删除异常',
				          message: '错误'
				        });
					})
		        })
			},
			// 保存或修改信息
			saveOrUpdate(){
				axios.post('/grade/saveOrUpdate',this.GDialog.form)
				.then(()=>{
					this.$notify.success({
			          title: '提交成功',
			          message: '成功'
			        });
			        this.findAllgrade();
					this.GDialog.visible=false;
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
				this.GDialog.visible=false;
				this.GDialog.form={};
			},
			// 添加
			toAddgrade(){
				this.GDialog.title='添加年级信息'
				this.GDialog.visible=true;
				this.GDialog.form={};
			},
			// 查询功能
			findAllgrade(){
				this.loading=true
				axios.get('/grade/findAll')
				.then(({data:result})=>{
					this.grades=result.data;
					// if (this.grades.descriptioin==null) {
					// 	this.grades.descriptioin='-'
					// }
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
	.grade_tbl i.fa{
		margin-right: 15px;
		font-size: 18px;
		cursor: pointer;
	}
	.grade_tbl i.fa-pencil{
		color: #67C23A;

	}
	.grade_tbl i.fa-trash{
		color: #F56C6C;

	}

</style>