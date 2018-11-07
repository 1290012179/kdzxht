<template>
	<div class="user">
		<div class="btns">
			<el-button size="mini" @click="toAddUser()">添加用户</el-button>
		</div>
		<div class="content" v-loading="loading">
		    <el-col :span="5" v-for="u in users" :key="u.id" class="everyuser">
	    		<img :src="u.userface?u.userface:userface" style="width: 100px;height: 100px; border-radius: 50%">
		    	<div class="use">
			    	<div>
				    	<span>用户名:</span>
				    	<span>{{u.username}}</span>
				    </div>
				    <div>
				    	<span>真实姓名:</span>
				    	<span>{{u.nickname}}</span>
				    </div>
				    <div>
				    	<span>注册时间:</span>
				    	<span>{{u.regTime?u.regTime:'--'}}</span>
				    </div>
				    <div>
				    	<span>邮箱:</span>
				    	<span>{{u.email}}</span>
				    </div>
				    <div>
				    	<span>状态:</span>
				    	<el-switch v-model="u.enabled" active-color="#13ce66" inactive-color="#ff4949"></el-switch>
				    </div>
				    <div>
				    	<span>操作:</span>
						<i class="fa fa-trash" @click="deleteUserById(u.id)"></i>
				    </div>
		    	</div>
			</el-col>
		</div>
		<div class="model">
			<el-dialog :title="userDialog.title" :visible.sync="userDialog.visible">
			  <el-form :model="userForm" ref="userForm" :rules="rules"  status-icon>
			    <el-form-item prop="username" label="用户名" label-width="120px">
			      <el-input v-model="userForm.username" autocomplete="off"></el-input>
			    </el-form-item>
			    <el-form-item prop="password" label="密码" label-width="120px">
			      <el-input type="password" v-model="userForm.password" autocomplete="off"></el-input>
			    </el-form-item>
			    <el-form-item prop="password1" label="确认密码" label-width="120px">
			      <el-input type="password" v-model="userForm.password1" autocomplete="off"></el-input>
			    </el-form-item>
			    <el-form-item prop="nickname" label="真实姓名" label-width="120px">
			      <el-input v-model="userForm.nickname" autocomplete="off"></el-input>
			    </el-form-item>
			    <el-form-item prop="email" label="邮箱" label-width="120px">
			      <el-input v-model="userForm.email" autocomplete="off"></el-input>
			    </el-form-item>
			  </el-form>
			  <div slot="footer" class="dialog-footer">
			    <el-button size="small" @click="closeUserDialog('userForm')">取 消</el-button>
			    <el-button type="primary" size="small" @click="saveOrUpdateUser('userForm')">确 定</el-button>
			  </div>
			</el-dialog>
		</div>
	</div>
</template>
<script>
	import axios from '@/http/axios'
	export default {
		data(){
			var validatePass = (rule, value, callback) => {
		        if (value === '') {
		          callback(new Error('请输入密码'));
		        } else {
		          if (this.userForm.password1 !== '') {
		            this.$refs.userForm.validateField('password1');
		          }
		          callback();
		        }
		      };
		      var validatePass2 = (rule, value, callback) => {
		        if (value === '') {
		          callback(new Error('请再次输入密码'));
		        } else if (value !== this.userForm.password) {
		          callback(new Error('两次输入密码不一致!'));
		        } else {
		          callback();
		        }
		      };
			return {
				imageUrl:'',
				userface:'https://ss1.bdstatic.com/70cFuXSh_Q1YnxGkpoWK1HF6hhy/it/u=2328144574,80036497&fm=27&gp=0.jpg',
				users:[],
				userDialog:{
					title:'',
					radio:1,
					visible:false,
				},
				userForm:{}, 
				rules: {
		            username: [
			            { required: true, message: '请输入用户名', trigger: 'blur' },
			            { min: 1, max: 6, message: '长度在 1 到 6 个字符', trigger: 'blur' }
		            ],
		            password:[
			            { validator: validatePass, trigger: 'blur'}
		            ],
		            password1:[
			            { validator: validatePass2, trigger: 'blur'}
		            ],
		            nickname: [
		                { required: true, message: '请输入真实姓名', trigger: 'blur' },
			            { min: 1, max: 4, message: '长度在 1 到 4 个字符', trigger: 'blur' }
		            ],
		            email: [
		                { required: true, message: '请输入邮箱', trigger: 'blur' },
		            ],
		        },
				loading:false,
			}
		},
		created(){
			this.findAllUsers();
		},
		methods:{
			deleteUserById(id){
				this.$confirm('此操作将永久删除该用户, 是否继续?', '提示', {
		            confirmButtonText: '确定',
		            cancelButtonText: '取消',
		            type: 'warning'
		        }).then(() => {
				let url = '/manager/user/deleteUserById?id=' + id;
		            axios.get(url).then(({data:result})=>{
	            		if(result.status == 200){
	            			this.$notify({
		            			title:'成功',
			            		message: result.message,
			            		type: '删除成功',
			            	});
	            		} else {
	            			this.$notify({
		            			title: '错误',
		            			message: '无法删除',
			            	});
	            		}
		            	this.findAllUsers();
		            });
		        }).catch(() => {
		            this.$message({
		                type: 'info',
		                message: '已取消删除'
		            });          
		        });
			},
			closeUserDialog(formName){
				this.userDialog.visible = false;
				if(formName){
					this.$refs[formName].resetFields();
				}
			},
			saveOrUpdateUser(formName){
				this.$refs[formName].validate((valid) => {
		            if (valid) {
						let url = '/manager/user/saveOrUpdateUser';
						axios.post(url,this.userForm)
						.then(({data:result})=>{
							if(result.status == 200){
								this.closeUserDialog();
								this.$notify({ 
									title: '成功',
						            message: result.message,
						            type: 'success'
						        });
								this.findAllUsers();
							} else {
								this.$notify({
						            title: '错误',
						            message: result.message
						        });
							}
						})
						.catch((error)=>{
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
			toAddUser(){
				this.userForm = {}; 
				this.userDialog.title = '添加用户';
				this.userDialog.visible = true;
			},
			findAllUsers(){
				this.loading = true;
				let url = '/manager/user/findAllUser';
				axios.get(url).then(({data:result})=>{
					this.users = result.data;
				}).catch((error)=>{
					console.log('错误:',error);
				}).finally(()=>{
					this.loading = false;
				});
			}
		}
	}
</script>
<style scoped>
.content {
	padding: 1em 3em;
	overflow: hidden;
	margin-left: 10px;
}
.everyuser {
	background-color: #f0f0f0;
	margin: 1em 1em;
	padding: .5em;
	text-align: center;
}
.use {
	width: 180px;
	text-align: left;
	margin: 0 auto;
	overflow-x: hidden; 
}
.use span {
	line-height: 24px;
}
i.fa {
	margin: 0 .3em;
	cursor: pointer;
}
i.fa.fa-trash {
	color: #f56c6c;
}
</style>