<template>
	<view class="page">
		<my-navbar title="修改密码" left="left" text="返回"></my-navbar>
		<view class="login-content">
			<view class="login-title">
				修改密码
			</view>
			<view class="iphone">
				<input type="" placeholder="请输入旧密码" :value="oldPasswordValue" @input="clearInput" />
				<uni-icons type="closeempty" color="#808080" size="25" v-if="showClearIcon" @click="clearIcon"></uni-icons>
			</view>
		 
			<view class="password">
				<input placeholder="请输入新密码" v-model="newPasswordValue" :password="showPassword" />
				<uni-icons type="eye-filled" color="#808080" size="25" @click="changePassword"></uni-icons>
			</view>
			<view class="login-btn" @click="Login()">立即修改</view>
		</view>
	</view>
</template>
 <script>
 	export default {
 		data() {
 			return {
 				oldPasswordValue: '', //手机号码
 				newPasswordValue: '', //密码
 				showPassword: true, //是否显示密码
 				showClearIcon: false, //是否显示清除按钮
 			}
 		},
  
 		methods: {
 			// 显示隐藏密码
 			changePassword: function() {
 				this.showPassword = !this.showPassword;
 			},
 			// 判断是否显示清除按钮
 			clearInput: function(event) {
 				this.oldPasswordValue = event.detail.value;
 				if (event.detail.value.length > 0) {
 					this.showClearIcon = true;
 				} else {
 					this.showClearIcon = false;
 				}
 			},
 			// 清除内容/隐藏按钮
 			clearIcon: function() {
 				this.oldPasswordValue = '';
 				this.showClearIcon = false;
 			},
 			// 密码登录
 			Login() {
 				if (!this.oldPasswordValue) {
 					uni.showToast({
 						title: '请输入旧密码',
 						icon: 'none'
 					})
 					return false
 				}
				
 				if (!this.newPasswordValue) {
 					uni.showToast({
 						title: '请输入新密码',
 						icon: 'none'
 					})
 					return false
 				}
				
				if (this.oldPasswordValue == this.newPasswordValue) {
					uni.showToast({
						title: '新密码不能与旧密码相同',
						icon: 'none'
					})
					return false
				}
				
				this.$u.api.editPassword({
					old_password:this.oldPasswordValue,
					password:this.newPasswordValue
				}).then( res => {
					uni.showToast({
						title: '更新成功',
						duration: 2000,
					});
					setTimeout(() => {
						uni.navigateBack({
							delta: 1
						});
					},2000)
				})
 			},
 		}
 	}
 </script>
<style scoped>
	.login-content {
		padding: 200px 20px 35px;
		text-align: center;
		color: #333333;
	}
 
	.login-title {
		font-size: 20px;
		font-weight: bold;
		margin-bottom: 31px;
	}
 
	.login-content input {
		height: 45px;
		background: #F8F8F8;
		border-radius: 25px;
		text-align: left;
		padding: 15px;
		box-sizing: border-box;
		font-size: 15px;
	}
 
	.iphone,
	.password,
	.test {
		position: relative;
		margin-bottom: 30px;
	}
 
	.iphone .uni-icons,
	.password .uni-icons {
		position: absolute;
		top: 8px;
		right: 30px;
	}
 
	.test-btn,
	.password-btn {
		color: #007aff;
		font-size: 15px;
		text-align: right;
	}
 
	.get-test {
		color: #007aff;
		font-size: 15px;
		width: 122px;
		height: 40px;
		border: 1px solid #007aff;
		border-radius: 25px;
		line-height: 40px;
	}
 
	.test {
		display: flex;
		justify-content: space-between;
	}
 
	.login-btn {
		width: 100%;
		height: 40px;
		background-image: linear-gradient(45deg,#007aff,#019eff);
		border-radius: 36px;
		color: #fff;
		font-size: 16px;
		text-align: center;
		line-height: 40px;
		margin-top: 50px;
	}
	page{
	    background: url(/static/images/bodybg.png) top no-repeat;
	    background-size: 100%;
	}
	
</style>