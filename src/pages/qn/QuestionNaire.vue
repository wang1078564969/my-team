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
				:data="questiondata"
				@selection-change="handleSelectionChange">
				<el-table-column
				type="selection"
				width="55">
				</el-table-column>
				<el-table-column
				label="#"
				width="80"
				prop="id">
				</el-table-column>
				<el-table-column
				label="问卷名称"
				width="150"
				prop="name">
				</el-table-column>
				<el-table-column
				label="简介"
				width="300"
				prop="description">
				</el-table-column>
				<el-table-column
				label="操作"
				width="140">
				<template slot-scope="scope">
					<el-button type="text" @click='oncheck(scope.row)'>查看</el-button>
					<el-button type="text" @click='onupdate(scope.row)'>修改</el-button>
					<el-button type="text" @click='adddelete(scope.row)'>删除</el-button>
				</template>
				</el-table-column>
			</el-table>
		</div>
		<!-- 模态框 -->
		<el-dialog
			:title="questionlog.title"
			:visible.sync="questionlog.visible"
			width="40%">
			<div class="description">{{allquestiondata.description}}</div>
			<div  v-for="c in allquestiondata.questionVMs" :key="c.id">
				<div class="question"> &nbsp; &nbsp; &nbsp; &nbsp; {{c.id}} . {{c.name}}</div>
				<div v-for="o in c.options" :key="o.id">
					<div class="option">{{o.label}} . {{o.name}}</div>
				</div>
			</div>
			<span slot="footer" class="dialog-footer">
				<el-button @click="questionlog.visible = false">取 消</el-button>
			</span>
		</el-dialog>
		<!-- 修改与添加 -->
		<el-dialog :title="this.questionlog2.title" :visible.sync="questionlog2.visible" width="40%">
			<el-form label-position="left" label-width="80px" :model="questionlog2.form">
				<el-form-item label="题目">
					<el-input v-model='questionlog2.form.name'></el-input>
				</el-form-item>
				<el-form-item label="简介">
					<el-input v-model='questionlog2.form.description'></el-input>
				</el-form-item>
				<!-- <el-form-item label="选项" v-for="c in this.questionlog.form.questionVMs" :key="c.id">
					<el-input v-model='questionlog2.form.options.name'></el-input>
				</el-form-item> -->
			</el-form>
			<span slot="footer" class="dialog-footer">
				<el-button @click="questionlog2.visible = false">取 消</el-button>
				<el-button type="primary" @click='updatedate()'>保 存</el-button>
			</span>
		</el-dialog>
	</div>
</template>
<script>
import getAxios from '@/http/axios'
let axios = getAxios();
	export default {
		data(){
			return{
				multipleSelection:[],
				//加载的问卷信息
				questiondata:[],
				//id查找的数据
				allquestiondata:[],
				//题库信息
				coursedata:[],
				//模态框1
				questionlog:{
					title:'',
					form:{},
					visible:false
				},
				//
				questionlog2:{
					title:'',
					form:{},
					visible:false
				},
			}
		},
		methods:{
			handleSelectionChange(val) {
				this.multipleSelection = val;
			},
			//打开查看
			oncheck(row){
				this.findquestion(row.id);
				this.questionlog.form=row;
				this.questionlog.title=this.questionlog.form.id+' . '+this.questionlog.form.name;
				this.questionlog.visible=true;
			},
			//打开修改
			onupdate(row){
				this.findquestion(row.id);

				this.questionlog2.form=this.allquestiondata
				this.questionlog2.title='修改'
				this.questionlog2.visible=true;
			},
			//打开添加
			onadd(){

			},
			//修改保存
			updatedate(){
				console.log(1);
				
			},
			//单个删除
			adddelete(row){
				this.$confirm('此操作将永久删除该年级, 是否继续?', '提示', {
		          	confirmButtonText: '确定',
		          	cancelButtonText: '取消',
		          	type: 'warning'
		        })
		        .then(()=>{
		        	axios.get('/questionnaire/deleteQuestionnaireById?id='+row.id)
					.then(()=>{
						this.$notify.success({
				          	title: '删除成功',
				          	message: '成功'
				        });
				        this.getquestiondata();
					})
					.catch(()=>{
						this.$notify.error({
				          	title: '删除异常',
				          	message: '错误'
				        });
					})
		        })
			},
			//id查找问卷信息
			findquestion(id){
				axios.get('/questionnaire/findQuestionnaireVMById?id='+id)
				.then((result) => {
					this.allquestiondata=result.data.data;
				}).catch((err) => {
					this.$notify.error({
	          			title: '错误',
	          			message: '网络异常！'
	        		});
				});
			},
			//获取问卷数据
			getquestiondata(){
				axios.get('/questionnaire/findAllQuestionnaire')
				.then((result) => {
					this.questiondata=result.data.data;
				}).catch((err) => {
					this.$notify.error({
	          			title: '错误',
	          			message: '网络异常！'
	        		});
				});
			},
			//获取题库
			getcoursedata(){
				axios.get('/question/findAllQuestionVM')
				.then((result) => {
					this.coursedata=result.data.data;
				}).catch((err) => {
					this.$notify.error({
	          			title: '错误',
	          			message: '网络异常！'
	        		});
				});
			},
		},
		created(){
			this.getquestiondata();
			this.getcoursedata();
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
	.el-dialog__body {
    padding: 0 2em 0 2em;
	}
	.description{
		margin-bottom: 2em;
		margin-left:18px;
	}
	/* .name{
		font-size:18px;
		margin: 8px;
	}
	.option{
		font-size: 16px;
		margin: 3px
	} */

</style>