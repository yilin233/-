<template>
	<view class="page">
		<my-navbar title="欧程CRM管理系统"></my-navbar>
		<view class="content">
		<uni-notice-bar v-if="notice" scrollable="true" :show-close="true" :show-icon="true" :single="true" :text="notice"/>
			<main-menu></main-menu>
			<view class="statement">
				<view class="title">数据报表</view>
				<view class="data-time">
					<view class="left" @click="showStartTime =  true">选择开始时间 {{start_time}}</view>
					<view class="right" @click="showEndTime = true">选择结束时间 {{end_time}}</view>
				</view>
				<view class="statement-data">
					<view class="subtitle">数据简报</view>
					<view class="statement-list">
						<u-grid :col="3">
							<u-grid-item v-for="(item,index) in list" :key="index">
								<view class="text1">{{item.amount}}</view>
								<view class="text2">{{item.title}}</view>
							</u-grid-item>
						</u-grid>
					</view>
				</view>
				<view class="custom-data">
					<view class="subtitle">个人保护库</view>
					<view class="protect-time">保护期：{{protect_days}}</view>
					<view class="custom-data-info">
						<view class="text">
							<view class="data">总容量：{{protect_num}}</view>
							<view class="data">已使用：{{used}}</view>
							<view class="data">剩余：{{surplus}}</view>
						</view>
						<view class="analyse">
							<u-circle-progress type="primary" active-color="#3986f4" :percent="scaleValue"
								duration="2000" width="300">{{scale}}</u-circle-progress>
						</view>
					</view>
				</view>
			</view>
		</view>
		<u-picker v-model="showStartTime" mode="time" :default-time="startDefaultTime" :params="params" @confirm="startTimeConfirm"></u-picker>
		<u-picker v-model="showEndTime" mode="time" :default-time="endDefaultTime" :params="params" @confirm="endTimeConfirm"></u-picker>
	</view>
</template>

<script>
	import mainMenu from '../components/main-menu.vue'
	export default {
		components: {
			mainMenu
		},
		data() {
			return {
				protect_days: "",
				notice: "",
				used: "",
				surplus: "",
				protect_num: "",
				scale: "",
				scaleValue: 0,
				start_time:"",
				end_time:"",
				list:[],
				params: {
					year: true,
					month: true,
					day: true,
					hour: false,
					minute: false,
					second: false
				},
				showStartTime:false,
				startDefaultTime:"",
				showEndTime:false,
				endDefaultTime:"",
			}
		},
		onLoad() {
			this.start_time = this.$u.timeFormat(new Date(), 'yyyy-mm') + '-01'
			this.end_time = this.$u.timeFormat(new Date(), 'yyyy-mm-dd')
			this.getNotive()
		},
		onShow() {
			this.getCustomerLib()
			this.getStatistic()
		},
		onReady() {
			this.startDefaultTime = this.start_time
			this.endDefaultTime = this.end_time
		},
		methods: {
			startTimeConfirm(e){
				const time =  e.year + '-' + e.month + '-' + e.day;
				if (time > this.end_time) {
					this.$u.toast('开始时间不能大于结束时间');
					return;
				}
				this.start_time = time;
				this.startDefaultTime = time
				this.getStatistic()
			},
			endTimeConfirm(e){
				this.end_time = e.year + '-' + e.month + '-' + e.day
				this.endDefaultTime = this.end_time
				this.getStatistic()
			},
			getStatistic() {
				this.$u.api.statistic({
					start_time:this.start_time,
					end_time:this.end_time
				}).then(res => {
					this.list = res
				})
			},
			getCustomerLib() {
				this.$u.api.customerLib().then(res => {
					this.protect_days = res.protect_days
					this.used = res.used
					this.surplus = res.surplus
					this.protect_num = res.protect_num
					this.scale = res.scale
					this.scaleValue = Number(res.scale.replace('%', ''))
				})
			},
			getNotive(){
				this.$u.api.indexNotice().then(res => {
					this.notice=res[0]
				})
			}
		}
	}
</script>

<style scoped lang="scss">
	.statement {
		margin-top: 20rpx;
		background-color: #fff;
		padding: 30rpx;

		.statement-data {
			margin: 20rpx 0;
			padding: 30rpx;
			border-radius: 16rpx;
			box-shadow: 0px 10rpx 10rpx 0px #b5bacb;
			border: 2rpx solid #eee;
		}

		.statement-list {
			margin-top: 20rpx;

			.text2 {
				font-size: 28rpx;
				margin-top: 24rpx;
				color: rgba(0, 0, 0, 0.8);
			}

			.text2 {
				color: rgba(0, 0, 0, 0.8);
			}
		}

		.custom-data {
			padding: 30rpx;
			border-radius: 16rpx;
			box-shadow: 0px 10rpx 10rpx 0px #b5bacb;
			border: 2rpx solid #eee;
			position: relative;

			.protect-time {
				position: absolute;
				top: 30rpx;
				right: 30rpx;
				color: rgba(0, 0, 0, 0.4);
			}

			.custom-data-info {
				display: flex;
				justify-content: space-between;
				align-items: center;
				margin: 20rpx 0;

				.data {
					margin: 40rpx 0;
					color: rgba(0, 0, 0, 0.7);
				}
			}
		}
	}

	.data-time {
		padding: 20px 0px 30px 0px;
		font-size: 12px;
	}

	.data-time .left {
		float: left;
	}

	.data-time .right {
		float: right;
	}
	.uni-noticebar{margin-bottom: 0px;}
	.data-time .left{
	background: #51a4ff;
    padding: 6px;
    color: #fff;
    border-radius: 5px;}
	.data-time .right{
	background: #9a9a9a;
	padding: 6px;
	color: #fff;
	border-radius: 5px;}
</style>