<template>
	<view class="login-content">
		<view class="login-title">
			欧程助贷CRM管理系统
		</view>
		<view class="iphone">
			<input type="" placeholder="请输入账号" :value="iphoneValue" @input="clearInput" class="name" style="padding-left: 35px;"/>
			<uni-icons type="closeempty" color="#808080" size="25" v-if="showClearIcon" @click="clearIcon"></uni-icons>
		</view>
		<view class="password">
			<input placeholder="请输入密码" v-model="passwordValue" :password="showPassword" class="pass" style="padding-left: 35px;"/>
			<uni-icons type="eye-filled" color="#808080" size="25" @click="changePassword"></uni-icons>
		</view>

		<view class="login-btn" @click="Login()">登录</view>
	</view>
</template>
 <script>
 	export default {
 		data() {
 			return {
 				iphoneValue: '', //手机号码
 				passwordValue: '', //密码
 				showClearIcon: false, //是否显示清除按钮
				showPassword: true, //是否显示密码
 			}
 		},
  
 		methods: {
			changePassword: function() {
				this.showPassword = !this.showPassword;
			},
 			// 判断是否显示清除按钮
 			clearInput: function(event) {
 				this.iphoneValue = event.detail.value;
 				if (event.detail.value.length > 0) {
 					this.showClearIcon = true;
 				} else {
 					this.showClearIcon = false;
 				}
 			},
 			// 清除内容/隐藏按钮
 			clearIcon: function() {
 				this.iphoneValue = '';
 				this.showClearIcon = false;
 			},
 			// 密码登录
 			Login() {
				if (!this.iphoneValue) {
					uni.showToast({
						title: '请输入用户名',
						icon: 'none'
					})
					return;
				}
				
				//if (!this.$u.test.mobile(this.iphoneValue)){
					//uni.showToast({
						//title: '账号格式不正确',
						//icon: 'none'
					//})
					//return;
				//}
				
				if (!this.passwordValue) {
					uni.showToast({
						title: '请输入密码',
						icon: 'none'
					})
					return;
				}
				
				this.$u.api.doLogin({
					mobile:this.iphoneValue,
					password:this.passwordValue
				}).then( res => {
					uni.showToast({
						title: '登录成功',
						duration: 2000,
					});
					setTimeout(() => {
						uni.switchTab({
							url:'/pages/index/index'
						});
					},2000)
				})
				
 			},
 		}
 	}
 </script>
<style scoped lang="scss">
	page{
	    background: url(/static/images/bodybg.png) top no-repeat;
	    background-size: 100%;
	}
	.login-content {
		padding: 225px 20px 35px;
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
		margin-top: 60px;
	}
	.iphone .name{
		    background: url(/static/images/name.png) left 0.8rem center no-repeat;
		    background-size: 0.8rem 1.2rem;
		    border: 1px solid #e4e4e4;
		}
	.login-content .pass{
			    background: url(/static/images/pass.png) left 0.8rem center no-repeat;
			    background-size: 0.8rem 1.2rem;
			    border: 1px solid #e4e4e4;
			}
</style>