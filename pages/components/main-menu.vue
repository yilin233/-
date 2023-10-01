<template>
	<view>
	  <view class="menu">
	  	<view class="title">常用功能</view>
	  	<view class="menu-list">
	  		<view class="menu-item" @click="go('/pages/custom/add')">
	  			<image src="/static/images/icon1.png" class="img"></image>
	  			<view class="grid-text">录入客户</view>
	  		</view>
			<view class="menu-item" @click="go('/pages/custom/visit')">
				<image src="/static/images/icon6.png" class="img"></image>
				<view class="grid-text">到访</view>
			</view>
	  		<view class="menu-item" @click="go('/pages/sign/add')">
	  			<image src="/static/images/icon2.png" class="img"></image>
	  			<view class="grid-text">签约</view>
	  		</view>
			<view class="menu-item" @click="go('/pages/custom/order')">
				<image src="/static/images/icon7.png" class="img"></image>
				<view class="grid-text">抢单</view>
			</view>
			<view class="menu-item" @click="goCustomerList()">
				<image src="/static/images/icon5.png" class="img"></image>
				<view class="grid-text">保护库</view>
			</view>
			<view class="menu-item" @click="go('/pages/index/compactList')">
				<image src="/static/images/icon8.png" class="img"></image>
				<view class="grid-text">合同</view>
			</view>
			<view class="menu-item" @click="go('/pages/index/myEntry')">
				<image src="/static/images/icon4.png" class="img"></image>
				<view class="grid-text">我的进件</view>
			</view>
	  		<view class="menu-item" @click="go('/pages/index/library')">
	  			<image src="/static/images/icon3.png" class="img"></image>
	  			<view class="grid-text">公海库</view>
	  		</view>
	  	</view>
	  </view>
	</view>
</template>

<script>
	export default{
		methods:{
			go(url){
				uni.navigateTo({
					url
				});
			},
			goCustomerList(){
				uni.switchTab({
					url:'/pages/custom/index'
				});
			}
		}
	}
</script>

<style scoped lang="scss">
	.menu{
		background-color: #fff;
		padding: 30rpx;
		.menu-list{
			display: grid;
			grid-template-columns: repeat(4, 1fr);
			gap: 1rem;
			justify-content: space-between;
			margin-top: 20rpx;
			.menu-item{
				display: flex;
				flex-direction: column;
				align-items: center;
			}
			.img{
				border-radius: 6rpx;
				width: 100rpx;
				height: 100rpx;
				color: #fff;
				margin-bottom: 10rpx;
				font-size: 20rpx;
				line-height: 100rpx;
				text-align: center;
			}
			.grid-text{
				font-size: 24rpx;
				color: rgba(0, 0, 0, 0.8);
			}
		}
	}
</style>