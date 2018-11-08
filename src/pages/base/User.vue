<template>
	<div class="user">

		<div class="btns">
			<el-input
				clearable
				style="width:240px"
				size="mini"
				placeholder="请输入用户姓名关键字"
				prefix-icon="el-icon-search"
				v-model="keywords">
			</el-input>
			<el-button size="mini" @click="toAddUsers" type="primary" plain>新增用户</el-button>
			<el-button size="mini" type="danger" @click="batchDeleteUsers" plain>批量删除</el-button>
		</div>
		<!-- 表格 -->
		 <div class="user_tbl" v-loading='loading'>
			 <el-table
				:data="users.slice((page-1)*pageSize,page*pageSize)"
				style="width: 100%"
				@selection-change="handleSelectionChange">
				<el-table-column
				type="selection"
				width="55">
				</el-table-column>
				<el-table-column
				fixed
				prop="name"
				label="姓名"
				width="150">
				</el-table-column>
				<el-table-column
				prop="gender"
				label="性别"
				width="120">
				</el-table-column>
				<el-table-column
				prop="type"
				label="职业"
				width="120">
				</el-table-column>
				<el-table-column
				prop="birth"
				label="出生日期"
				width="300">
				</el-table-column>
				<el-table-column
				prop="hiredate"
				label="入校时间"
				width="300">
				</el-table-column>
				<el-table-column
				fixed="right"
				label="操作"
				width="120">
					<template slot-scope='{row}'>
						<i class="fa fa-trash" @click='deleteUser(row.id)'></i>
						<i class="fa fa-pencil" @click='toUpdateUser(row)'></i>
					</template>
				</el-table-column>
			</el-table>
		</div>
		<!-- 表格结束 -->
	<!-- 模态框 -->
		<el-dialog :title="Udialog.title" :visible.sync="Udialog.visible">
		<el-form :model="Udialog.form" :rules="Udialog.rules" ref="userForm" label-width="80px">
			<el-form-item label="姓名" prop="name">
			<el-input v-model="Udialog.form.name"></el-input>
			</el-form-item>
			<el-form-item label="性别" prop="gender">
				<el-radio-group v-model="Udialog.form.gender">
				<el-radio label="男"></el-radio>
				<el-radio label="女"></el-radio>
				</el-radio-group>
			</el-form-item>
			<el-form-item label="职业" prop="type" >
			<el-select v-model="Udialog.form.type" placeholder="请选择职业">
				<el-option label="老师" value="老师"></el-option>
				<el-option label="学生" value="学生"></el-option>
			</el-select>
			</el-form-item>
			<el-form-item label="入校时间">
				<el-col :span="11">					
				<el-date-picker type="date" placeholder="选择日期" v-model="Udialog.form.hiredate" style="width: 100%;"  format="yyyy 年 MM 月 dd 日"
      value-format="yyyy-MM-dd"></el-date-picker>
				</el-col>
			</el-form-item>
			<el-form-item label="出生日期">
				<el-col :span="11">
				<el-date-picker type="date" placeholder="选择日期" v-model="Udialog.form.birth" style="width: 100%;"  format="yyyy 年 MM 月 dd 日"
      value-format="yyyy-MM-dd"></el-date-picker>
				</el-col>
			</el-form-item>
		</el-form>
		<div slot="footer" class="dialog-footer">
			<el-button @click="closeUdialog">取 消</el-button>
			<el-button type="primary" @click="SaveOrUpdataUser">确 定</el-button>
		</div>
		</el-dialog>
		<!-- 分页 -->
		<div class="page">
			 <el-pagination
			 	@current-change='handleCurrentChange'
				:page="page"
			 	:page-size="pageSize"
				layout="prev, pager, next"
				:total="total">
			</el-pagination>
		</div>
	</div>

</template>
<script>
import getAxios from '@/http/axios'
let axios = getAxios();
  export default {
    data() {
      return {
		loading:false,
		users:[],
		// 复选框时element里的
		multipleSelection:[],
		page:1,
		pageSize:5,
		total:'',
		keywords:'',
		Udialog:{
			title:'',
			visible:false,
			   form: {},
			//    表单验证
			   rules:{
				    name: [
						{ required: true, message: '请输入您的姓名', trigger: 'blur' },
						{ min: 2, max: 5, message: '长度在 2 到 5 个字符', trigger: 'blur' }
					],
					type: [
						{ required: true, message: '请选择职业', trigger: 'change' }
					],
					gender: [
						{ required: true, message: '请选择性别', trigger: 'change' }
					],	
			   }
		},	
      };
	},
	watch:{
		// 监听关键字变化
		keywords:{
				handler:function(){
					this.findkeywords();
				},
				deep:true
				// 深度监听
			}
	},
	created(){
            this.findAllUsers();
        },
	methods:{
		// 分页
		handleCurrentChange(page){
			this.page=page;
		},
		// 关键字查询
		findkeywords(){
			let key=this.keywords
			if(key){
				axios.get('/user/query',{
					// 将获取到的值传到params中
					params:{
						keywords:key
					}
				})
				.then(({data:result})=>{
					this.users = result.data	
				})
				.catch(()=>{
					
				})
			}else{
				this.findAllUsers();
			}
			
		},
		// 复选框时element里的方法
		handleSelectionChange(val){
			this.multipleSelection = val;
		},
		//修改用户
		toUpdateUser(row){
			this.Udialog.title='修改信息';
			this.Udialog.form=row;
			this.Udialog.visible=true;
		},
		//批量删除
		batchDeleteUsers(){
			this.$confirm('此操作将永久删除该文件，是否继续?','提示',{
				confirmButtonText:'确定',
				cancelButtonText:'取消',
				type:'warning'
			})
			.then(()=>{
				let ids = this.multipleSelection.map((item)=>{
				return item.id;
			});
				axios.post('/user/batchDelete',{ids})
				.then(()=>{
					this.$message({
						type:'success',
						message:'删除成功!',
					});
					this.findAllUsers();
				})
				.catch(()=>{
					this.$message({
						type:'info',
						message:'已取消删除'
					});
				})
			})
		},
		// 删除用户
		deleteUser(id){
			this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
		})
		.then(()=>{
			axios.get('/user/deleteById?id='+id)
			.then(() => {
          this.$message({
            type: 'success',
            message: '删除成功!'
		  });
		  this.findAllUsers();
		})
		.catch(() => {
          this.$message({
            type: 'info',
            message: '已取消删除'
          });          
        });
		})
		
		},
		// 确定按钮绑定的新增保存和修改保存
		SaveOrUpdataUser(){
			this.$refs.userForm.validate((valid)=>{
				if(valid){
					axios.post('/user/saveOrUpdate',this.Udialog.form)
					.then(()=>{
						this.$notify.success({
							title:'成功',
							message:'保存成功！'
						})
						this.closeUdialog();
						this.findAllUsers();		
					})
					.catch(()=>{
						this.$notify.error({
							title:'错误',
							message:'网络异常'
						});
					})
				}
			})
			
		},
		// 新增用户
		toAddUsers(){
			this.Udialog.title= '添加用户'
			this.Udialog.visible=true;
			this.Udialog.form={};
		},
		// 关闭模态框
		closeUdialog(){
			this.Udialog.visible=false; 
			this.Udialog.form={};
		},
		
		findAllUsers(){
			this.loading= true;
			axios.get('/user/findAll')
			.then(({data:result})=>{
				this.users= result.data;
				this.total=this.users.length;
			})
			.catch(()=>{
				this.$notify.error({
					title:'错误',
					message:'网络异常',
				});
			})
			.finally(()=>{
				this.loading=false;
			})
		},
	}
  };
</script>
  
<style>
.btns{
	float: right;
	margin-bottom: .5em;
}
i.fa {
		margin: 0 .3em;
		cursor: pointer;
	}
	i.fa.fa-trash {
		color: #F56C6C;
	}
</style>
