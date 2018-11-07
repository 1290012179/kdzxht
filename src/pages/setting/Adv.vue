<template>
	<div class="article">
		<div class="select">
			<el-select v-model="article.categoryId" placeholder="请选择" size="mini" @change="findArticle">
			    <el-option v-for="item in categories" :key="item.id" :label="item.name" :value="item.id"></el-option>
			</el-select>
			<el-button size="small" @click="toAddArticle()">添加</el-button>
			<el-button size="small" @click="batchDeleteArticle">批量删除</el-button>
			
		</div>
		<div class="table" v-loading='loading'>
			<el-table :border='true' size='small' :data="articles" style="width: 100%">
		      <el-table-column type="selection"width="55"></el-table-column>
		      <el-table-column prop="title"label="文章标题"width="100"></el-table-column>
		      <el-table-column prop="category.name"label="所属栏目"width="180"></el-table-column>
		      <el-table-column prop="liststyle"label="列表样式"width="180"></el-table-column>
		      <el-table-column prop="auther"label="作者"width="180"></el-table-column>
		      <el-table-column prop="publishtime"label="发布时间"width="180"></el-table-column>
		      <el-table-column prop="readtimes"label="阅读次数"></el-table-column>
		      <el-table-column label="操作" width='60'>
		      	<template slot-scope='{row}'>
		      		<i class="fa fa-trash" @click='deleteArticle(row.id)'></i>
		      		<i class="fa fa-pencil" @click='toAddArticle(row)'></i>
		      	</template>
		      </el-table-column>
		    </el-table>
		</div>
		<div class="model">
			<el-dialog :title="articleDialog.title" :visible.sync="articleDialog.visible">
			  <el-form :model="articleDialog.form" ref="articleDialog.form" :rules="rules">
			    <el-form-item prop="title" label="文章标题" label-width="120px">
			      <el-input v-model="articleDialog.form.title" autocomplete="off"></el-input>
			    </el-form-item>
			    <el-form-item label="所属栏目" label-width="120px">
			      <el-select v-model="articleDialog.form.categoryId" placeholder="请选择">
			        <el-option v-for="c in categories" :label="c.name" :key="c.id" :value="c.id"></el-option>
			      </el-select>
			    </el-form-item>
			    <el-form-item prop="liststyle" label="列表样式" label-width="120px">
			    	<el-radio v-model="articleDialog.form.liststyle" label="style-one"></el-radio>
				    <el-radio v-model="articleDialog.form.liststyle" label="style-two"></el-radio>
			    </el-form-item>
			    <el-form-item prop="content" label="描述信息" label-width="120px">
			      <el-input type="textarea" :rows="2" placeholder="请输入内容" v-model="articleDialog.form.content"></el-input>
			    </el-form-item>
			  </el-form>
			  <div slot="footer" class="dialog-footer">
			    <el-button size="small" @click="closeArticleDialog('articleDialog.form')">取 消</el-button>
			    <el-button type="primary" size="small" @click="saveOrUpdateArticle('articleDialog.form')">确 定</el-button>
			  </div>
			</el-dialog>
		</div>
	</div>
</template>
<script>
	import axios from '@/http/axios'
	export default{
		data(){
			return{
				categories:[],
				articles:[],
				ids:[],
				article:{
					page:0,
					pageSize:1000,
				},
				articleDialog:{
					title:'',
					radio:1,
					form:{},
					visible:false,
				},
				rules: {
		            title: [
			            { required: true, message: '请输入文章标题', trigger: 'blur' },
			            { min: 1, max: 8, message: '长度在 1 到 8 个字符', trigger: 'blur' }
		            ],
		            liststyle:[
			            { required:true, message: '请选择列表样式', trigger: 'change'}
		            ],
		            content: [
		                { required: true, message: '请输入描述信息', trigger: 'blur' }
		            ],
		        },
				loading:false,
			}
		},
		created(){
			this.findAllCategories();
		},
		methods:{
			handleSelectionChange(val) {
				this.ids = [];
				val.forEach((item)=>{
					this.ids.push(item.id);
				});
			},saveOrUpdateArticle(formName){
				 this.$refs[formName].validate((valid) => {
		            if (valid) {
		                let url = '/manager/article/saveOrUpdateArticle';
						axios.post(url,this.articleDialog.form).then(({data:result})=>{
							if(result.status == 200){
								this.closeArticleDialog();
								this.$notify({ 
									title: '成功',
						            message: result.message,
						            type: 'success'
						        });
								this.findArticle();
							} else {
								this.$notify({
						            title: '错误',
						            message: result.message
						        });
							}
						}).catch((error)=>{
							this.$notify({
								type:'error',
					            title: '错误',
					            message: '网络异常'
					        });
						});
		            } else {
		                return false;
		            }
		        });
			},
			batchDeleteArticle(){
				this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
		            confirmButtonText: '确定',
		            cancelButtonText: '取消',
		            type: 'warning'
		        }).then(() => {
					let url = '/manager/article/batchDeleteArticle';
		        	axios.post(url,{ids:this.ids}).then(({data:result})=>{
		        		if(result.status == 200){
	            			this.$notify({
		            			title:'成功',
			            		message: result.message,
			            		type: 'success',
			            	});
	            		} else {
	            			this.$notify({
		            			title: '发生错误',
		            			message: '后台数据异常',
			            	});
	            		}
		            	this.findArticle();
		            });
		        }).catch(() => {
		            this.$message({
		                type: 'info',
		                message: '已取消删除'
		            });          
		        });
			},
			deleteArticle(id){
				this.$confirm('此操作将永久删除该文章, 是否继续?', '提示', {
		            confirmButtonText: '确定',
		            cancelButtonText: '取消',
		            type: 'warning'
		        }).then(() => {
		            let url = '/manager/article/deleteArticleById?id='+id;
		            axios.get(url).then(({data:result})=>{
	            		if(result.status == 200){
	            			this.$notify({
		            			title:'成功',
			            		message: result.message,
			            		type: 'success',
			            	});
	            		} else {
	            			this.$notify({
		            			title: '错误',
		            			message: '无法删除',
			            	});
	            		}
		            	this.findArticle();
		            });
		        }).catch(() => {
		            this.$message({
		                type: 'info',
		                message: '已取消删除'
		            });          
		        });
			},
			closeArticleDialog(formName){
				this.articleDialog.visible = false;
				if(formName){
					this.$refs[formName].resetFields();
				}
			},
			toAddArticle(row){
					if(row){
						this.articleDialog.form = {
							id: row.id,
							title: row.title,
							liststyle: row.liststyle,
							content: row.content,
							categoryId: row.category.name,
						};
						this.articleDialog.title = '修改文章';
					} else {
						this.articleDialog.form = {
							categoryId: this.article.categoryId,
						};
						this.articleDialog.title = '添加文章';
					}
					this.articleDialog.visible = true;
			},
			findArticle(){
				this.loading = true;
				let url = '/manager/article/findArticle';
				axios.get(url,{params:this.article}).then(({data:result})=>{
					// 将查询到的数据绑定到模型中
					if(result.status == 200){
						this.articles = result.data.list;
					} else {
						alert('后台数据异常');
					}
				}).catch((error)=>{
					console.log('error',error);
				}).finally(()=>{
					this.loading = false;
				});
			},
			findAllCategories(){
					let url = '/manager/category/findAllCategory';
					axios.get(url).then(({data:result})=>{
						this.categories = result.data;
						// this.article.categoryId = this.categories[0].id;
					}).catch((error)=>{
						console.log('error:',error);
					});
			}
		}	
	}
</script>
<style scoped>
i.fa {
	margin: 0 .3em;
	cursor: pointer;
}
i.fa.fa-trash,i.fa.fa-pencil {
	color: #f56c6c;
}
.table {
	padding: 1em 0 0 0;
}
</style>
