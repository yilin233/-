<template>
	<view class="page">
		<my-navbar title="合同列表" left="left" text="返回"></my-navbar>
		<view class="content">
			<view class="search-wrap">
				<view class="search">
					<input class="uni-input" v-model="form.keywords"  placeholder="请输入合同号" />
					<image src="/static/images/icon-search.png" class="icon-search" @click="onSearch"></image>
				</view>
			</view>
			
			<view class="tabs">
				<view v-for="(item,index) in tabs" :key="index" :class="{'active':form.status == index}" class="tab-item" @click="changeTabs(index)">{{item}}</view>
			</view>
			<view class="list">
				<view class="item" v-for="(item,index) in list" :key="index" @tap="detail(item)">
					<view class="name">{{item.number}}</view>
					<view class="status">客户：{{item.customer}} </view>
					<view class="status-text" :style="{backgroundColor: item.front_status_text.color}">{{item.front_status_text.text}}</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default{
		data(){
			return {
				tabs:['全部','未签约','已签约'],
				form:{
					keywords:"",
					status:0,
				},
				status: 'loadmore',
				list:[]
			}
		},
		onShow() {
			this.getList()
		},
		methods:{
			onSearch(){
				this.list = [];
				this.getList()
			},
			getList(){
				this.$u.api.contractList(this.form).then( res => {
					this.list = res
				})
			},
			changeTabs(index){
				this.form.status = index
				this.list = [];
				this.getList()
			},
			detail(item){
				if(item.status <= 1) return;
				uni.navigateTo({
					url: "/pages/index/myEntry?number="+item.number
				})
			}
		}
	}
</script>

<style scoped lang="scss">
	.search-wrap{
		background-color: #fff;
		padding: 20rpx 40rpx;
		.search{
			display: flex;
			align-items: center;
			border: 2rpx solid #ccc;
			padding: 12rpx 20rpx;
			border-radius: 8rpx;
		}
		.icon-search{
			width: 48rpx;
			height: 48rpx;
		}
		.uni-input{
			flex: 1;
			height: 48rpx;
			font-size: 24rpx;
		}
	}
	.tabs{
		display: flex;
		border-radius: 8rpx;
		overflow: hidden;
		
		.tab-item{
			flex: 1;
			text-align: center;
			border: 2rpx solid #5595df;
			padding: 20rpx 0;
			font-size: 24rpx;
			color: #5595df;
			background-color: #fff;
		}
		.tab-item.active{
			background-color:  #5595df !important;
			color: #fff !important;
		}
		.tab-item:nth-child(2n){
			border-right: 0;
			border-left: 0;
		}
		.tab-item:nth-child(1){
			border-radius: 8rpx 0 0 8rpx;
		}
		.tab-item:last-child{
			border-radius: 0 8rpx 8rpx 0;
		}
		
	}
	
	.list{
		margin:  20rpx;
		.item{
			background-color: #fff;
			border-radius: 16rpx;
			padding: 30rpx;
			border: 2rpx solid #ccc;
			position: relative;
			margin-bottom: 20rpx;
			.status{
				margin-top: 20rpx;
				font-size: 24rpx;
				color: rgba(0, 0, 0, 0.7);
			}
			
			.status-text{
				position: absolute;
				top: 30rpx;
				right: 30rpx;
				color: #fff;
				font-size: 24rpx;
				border-radius: 8rpx;
				width: 150rpx;
				height: 56rpx;
				text-align: center;
				line-height: 56rpx;
				background-color: #a0d1f0;
			}
			.bg{
				background-color: #009688;
			}
		}
	}
</style>