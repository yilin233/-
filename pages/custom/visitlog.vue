<template>
	<view class="page">
		<my-navbar title="我的到访" left="left" text="返回"></my-navbar>
		<view class="content">
			<view class="search-wrap">
				<view class="search">
					<input class="uni-input" v-model="form.keywords"  placeholder="请输入客户名称搜索" />
					<image src="/static/images/icon-search.png" class="icon-search" @click="onSearch"></image>
				</view>
			</view>

			<view class="list">
				<view class="item" v-for="(item,index) in list" :key="index">
					<view class="name">到访时间：{{item.visit_time}}</view>
					<view class="status">客户名称：{{item.company_name}}</view>
					<view class="status">备注内容：{{item.content}}</view>
				</view>
			</view>
			<view>
				<u-loadmore :status="status" />
			</view>
		</view>
	</view>
</template>

<script>
	export default{
		data(){
			return {
				tabs:[
					{
						text:'全部',
						value:0
					},
					{
						text:'已放款',
						value:1
					},
					{
						text:'已批款',
						value:11
					},
					{
						text:'已拒绝',
						value:12
					},
					{
						text:'已结清',
						value:2
					}
					
				],
				currentIndex:0,
				form:{
					status:0,
					keywords:"",
					page:1,
					page_size:10
				},
				list:[],
				status: 'loadmore',
			}
		},
		onShow() {
			this.form.page = 1;
			this.list = [];
			this.getcustomerVisitLog()
		},
		methods:{
			onSearch(){
				this.form.page = 1;
				this.list = [];
				this.getcustomerVisitLog()
			},
			getcustomerVisitLog(){
				this.$u.api.customerVisitLog(this.form).then( res => {
					let newList = res
					this.list = this.list.concat(newList)
					
					if (res.length < this.form.page_size) {
						this.status = 'nomore'
					} else {
						this.status = 'loading'
						this.form.page += 1;
					}
				})
			},
			changeTabs(index){
				this.currentIndex = index
				this.form.status = this.tabs[index].value
				this.form.page = 1;
				this.list = [];
				this.getcustomerVisitLog()
			}
		},
		onReachBottom() {
			if (this.status == 'loading'){
				this.getcustomerVisitLog()
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
				font-size: 30rpx;
				color: rgba(0, 0, 0, 0.7);
				 white-space: pre-line;
				 line-height: 2;
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
				background-color: #FFB800;
			}
			
			.bg2{
				background-color: #f56c6c;
			}
			
			.bg0{
				background-color: #1E9FFF;
			}
		}
	}
</style>