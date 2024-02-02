<template>
	<view>
		<image src="../../static/logo-1.png" class="logo" mode="widthFix"></image>
		<input class="uni-input" v-model="userCode" focus placeholder="请输入账号" />
		<input class="uni-input" v-model="userPwd" password type="text" placeholder="请输入密码" />

		<button class="login-btn" open-type="getUserInfo" @tap="login()">登陆系统</button>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				userCode:"",
				userPwd:""
			}
		},
		methods: {
			toRegister:function(){
				uni.navigateTo({
					url:"../register/register"
				})
			},
			login:function(){
				let that=this
				console.log(that.userCode)
				console.log(that.userPwd)
				let data = {}
				data.userCode = that.userCode
				data.userPwd = that.userPwd
				
				that.ajax(that.url.login,"POST",data,function(resp){
					uni.setStorageSync("Token", resp.data.data)
					//跳转到登陆页面
					uni.switchTab({
						url:"../index/index"
					})
				})		
			}
		}
	}
</script>

<style lang="less">
	@import url("login.less");
	
</style>
