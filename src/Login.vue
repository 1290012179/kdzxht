<template>
	<div class="login">
		<h2>登录页面</h2>
		<el-form >
		    <el-form-item label="用户名" label-width="120px">
		      <el-input ></el-input>
		    </el-form-item>
		    <el-form-item label="密码" label-width="120px">
		    	<el-input ></el-input>
		    </el-form-item>
		    
		  </el-form>
		<el-button @click='login'>登录</el-button>
	</div>
</template>
<script>
	import axios from '@/http/axios'
	export default{
		data(){
			return{
				form:{

				}
			}
		},
		methods:{
			login(){
				axios.post('/login',this.form).then((result)=>{
					this.$root.currentComponent='App';
				}).catch((error)=>{
					console.log('error',error);
				});
				
			}
		}
	}
</script>
<style>
	.login{
		width: 36%;
		margin: 0 auto;
	}
</style>