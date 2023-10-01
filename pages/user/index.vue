<template>
	<view class="page" >
	   <my-navbar title="我的"></my-navbar>
		<view class="content">
			<view class="user-info">
				<image v-if="avatar"  class="avatar" :src="imageSrc(avatar)"></image>
				<view v-else class="avatar"></view>
				<view class="info">
					<view class="name">{{name}}-{{position}}</view>
					<view class="phone">{{organization}}</view>
					<view class="phone">{{mobile}}</view>
				</view>
			</view>
			<view class="menu">
				<view class="menu-item" @click="go('/pages/user/edit')">
					<view class="left">
						<image src="/static/images/icon-pwd.png"></image>
						<text>修改密码</text>
					</view>
					<u-icon name="arrow-right"></u-icon>
				</view>
				<view class="menu-item" @click="logout()">
					<view class="left">
						<image src="/static/images/icon-logout.png"></image>
						<text>退出登录</text>
					</view>
					<u-icon name="arrow-right"></u-icon>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default{
		data(){
			return {
				name:"",
				avatar:"",
				position:"",
				mobile:"",
				organization:"",
			}
		},
		onShow() {
			this.getUserInfo()
		},
		methods:{
			go(url){
				uni.navigateTo({
					url
				});
			},
			getUserInfo(){
				this.$u.api.getUserInfo().then( res => {
					this.name = res.name
					this.avatar = res.avatar
					this.position = res.position
					this.mobile = res.mobile
					this.organization = res.organization
				})
			},
			logout(){
				const that = this
				uni.showModal({
					title: '提示',
					content: '是否要退出登录',
					success: function (res) {
						if (res.confirm) {
							console.log('用户点击确定');
							that.$u.api.logout().then( res => {
								uni.removeStorageSync('token');
								uni.navigateTo({
									url:'/pages/user/login'
								});
							})
						} 
					}
				});
			}
		}
	}
</script>

<style scoped lang="scss">
	.user-info{
		padding: 40rpx;
		display: flex;
		align-items: center;
		border-bottom: 20rpx solid #efefef;
		
		.avatar{
			width: 160rpx;
			height: 160rpx;
			border: 2rpx solid #ccc;
			border-radius: 50%;
		}
		.info{
			margin-left: 30rpx;
		}
		
		.name{
			font-size: 36rpx;
			font-weight: bold;
		}
		
		.phone{
			color: rgba(0, 0, 0, 0.6);
			margin-top: 20rpx;
			font-size: 26rpx;
		}
	}
	.menu{
		margin-top: 20rpx;
		padding: 0 40rpx;
		.menu-item{
			display: flex;
			justify-content: space-between;
			align-items: center;
			padding: 32rpx 0;
			font-size: 26rpx;
			border-bottom: 2rpx solid #ccc;
			.left{
				display: flex;
				align-items: center;
				& image{
					width: 48rpx;
					height: 48rpx;
					margin-right: 16rpx;
				}
			}
		}
	}
</style>