<template>
	<view class="page">
		<my-navbar title="公海库" left="left" text="返回"></my-navbar>
		<view class="content">
			<view class="dropdown">
				<u-dropdown :close-on-click-mask="true" :borderBottom="false" ref="uDropdown">
					<u-dropdown-item style="background-color: #fff;padding-bottom: 10rpx;" title="标签">
						<view v-for="item in labels"  class="itemBox">
							<view class="label">{{item.name}}</view>
							<view class="value">
								<radio-group @change="radioChange1(item.name,$event)" v-if="item.select_type==1">
									<label class="list checkbox" v-for="(item1,index) in item.labels">
										<view>
											<radio :value="item1" :checked="item1==kaipiao||item1==nashui"/>
										</view>
										<view>{{item1}}</view>
									</label>
								</radio-group>
								<checkbox-group @change="checkboxChange(item.name,$event)" v-if="item.select_type==2">
									<label class="list checkbox" v-for="item1 in item.labels">
										<view>
											<checkbox :value="item1" :checked="zhengxin.some(item=>item==item1)||shangwulei.some(item=>item==item1)"/>
										</view>
										<view>{{item1}}</view>
									</label>
								</checkbox-group>
							</view>
						</view>
						<view class="btnBox">
							<view class="btn" @click="closeDropdown">
								提交
							</view>
							<view class="btn" style="background-color: #acacac;" @click="reset">
								重置
							</view>
						</view>
					</u-dropdown-item>
				</u-dropdown>
				<view class="search-wrap">
					<view class="search">
						<input v-model="form.keywords" class="uni-input" placeholder="请输入客户名称搜索" />
						<image src="/static/images/icon-search.png" class="icon-search" @click="onSearch"></image>
					</view>
				</view>
			</view>
			<view class="list">
				<view class="item" v-for="(item,index) in list" :key="index">
					<view class="item-header">
						<view class="title">{{item.company_name}}</view>
						<view class="tag">{{item.protect_text.text}}</view>
					</view>
					<view class="info">
						<view class="text">法人：{{item.name}}</view>
						<view class="text">联系电话：{{item.mobile | phoneFilter}}
						<view style="float: right;">到访次数：{{item.order_number}}</view>
						</view>
						<view class="text">加入日期：{{item.create_time}}</view>
						<view class="labels" style="margin-top: 10px;margin-bottom: 5px;">
							<view class="type" v-if="item.type == 1">企业客户</view>
							<view class="type person" v-else>个人客户</view>
							<view class="label" v-for="(label,index) in item.labels" :key="index">{{label}}</view>
						</view>
					</view>
					<view class="item-bottom">
						<image src="/static/images/icon-phone.png" @click="call(item)"></image>
						<image src="/static/images/icon-more.png" @click="showMenu(item)"></image>
					</view>
				</view>
			</view>
			<view>
				<u-loadmore :status="status" />
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				labels: [],
				form: {
					type: 0,
					range: "",
					order: 1,
					keywords: "",
					page: 1,
					page_size: 10,
					labels: []
				},
				list: [],
				status: 'loadmore',
				show: false,
				// 开票
				kaipiao: [],
				// 纳税
				nashui: [],
				// 征信
				zhengxin: [],
				// 商务类
				shangwulei: []
			}
		},
		onShow() {
			
		},
		onLoad() {
			this.getCustomerLabels()
			this.form.page = 1;
			this.list = [];
			this.getList()
		},
		methods: {
			reset(){
				this.kaipiao = []
				this.nashui = []
				this.zhengxin = []
				this.shangwulei = []
				this.closeDropdown()
			},
			closeDropdown() {
				this.$refs.uDropdown.close();
				this.form.labels = []
				this.form.labels = this.form.labels.concat(this.kaipiao, this.nashui, this.zhengxin, this.shangwulei)
				this.form.page = 1;
				this.list = [];
				this.getList()
			},
			checkboxChange(name, e) {
				if (name == '开票') {
					this.kaipiao = e.detail.value
				}
				if (name == '纳税') {
					this.nashui = e.detail.value
				}
				if (name == '征信') {
					this.zhengxin = e.detail.value
				}
				if (name == '商务类') {
					this.shangwulei = e.detail.value
				}
			},
			radioChange1(name, e) {
				if (name == '开票') {
					this.kaipiao = e.detail.value
				}
				if (name == '纳税') {
					this.nashui = e.detail.value
				}
				if (name == '征信') {
					this.zhengxin = e.detail.value
				}
				if (name == '商务类') {
					this.shangwulei = e.detail.value
				}
			},
			getCustomerLabels() {
				this.$u.api.customerLabels().then(res => {
					this.labels = res
					// consloe
				})
			},
			dropdownChange(e) {
				this.form.page = 1;
				this.list = [];
				this.getList()
			},
			onSearch() {
				this.form.page = 1;
				this.list = [];
				this.getList()
			},
			call(item) {
				uni.makePhoneCall({
					phoneNumber: item.mobile
				});
			},
			getList() {
				this.$u.api.seaCustomer(this.form).then(res => {
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
			showMenu(item) {
				const that = this
				uni.showActionSheet({
					itemList: ['放入我的保护库'],
					success: function(res) {
						if (res.tapIndex == 0) {
							that.onPickup(item)
						}
					},
					fail: function(res) {
						console.log(res.errMsg);
					}
				});
			},
			onPickup(item) {
				console.log('11', item)
				this.$u.api.customerPickup({
					customer_id: item.id
				}).then(res => {
					uni.showToast({
						title: '放入成功',
						duration: 2000,
					});
					this.form.page = 1;
					this.list = [];
					this.getList()
				})
			}
		},
		onReachBottom() {
			if (this.status == 'loading') {
				this.getList()
			}
		},
		// 加密手机号
		filters: {
			phoneFilter(val) {
				let reg = /^(.{3}).*(.{4})$/;
				return val.replace(reg, '$1****$2');
			}
		}
	}
</script>

<style scoped lang="scss">
	/deep/ .uni-navbar__header{background: #0ca039 !important;}
	.checkbox {
		width: 50%;
		box-sizing: border-box;
		margin: 2px 0;
	}

	/deep/ .u-dropdown__menu__item .u-flex {
		display: flex;
	}

	/deep/ .u-dropdown__menu__item {
		flex: inherit;
		width: 20%;
	}

	.dropdown {
		background-color: #fff;
		display: flex;
		align-items: center;
		position: relative;

		.icon-search {
			position: absolute;
			right: 24rpx;
			width: 64rpx;
			height: 100%;
			display: flex;
			align-items: center;
			z-index: 99;

			& image {
				width: 48rpx;
				height: 48rpx;
			}
		}

		.itemBox {
			display: flex;
			align-items: center;
			width: 100%;
			font-size: 24rpx;
			border-bottom: 1px solid #ececec;

			.value {
				width: 100%;
			}

			.label {
				width: 120rpx;
				text-align: center;
				flex-shrink: 0;
				padding-left: 20rpx;
			}

			.list {
				display: flex;
				align-items: center;
				padding: 14rpx;
			}
		}

		.btnBox {
			display: flex;
			justify-content: center;

			.btn {
				width: 200px;
				height: 34px;
				line-height: 34px;
				text-align: center;
				background-color: #5595df;
				color: #fff;
				font-size: 14px;
				border: none;
				margin: 20rpx;
				border-radius: 10rpx;
			}
		}

	}

	.list {
		position: relative;
		z-index: 1;
		padding: 20rpx;
	
		.item {
			background-color: #fff;
			border-radius: 16rpx;
			padding: 30rpx;
			border: 2rpx solid #ccc;
			position: relative;
			margin-bottom: 20rpx;
	
			.type {
				width: 130rpx;
				height: 45rpx;
				font-size: 12px;
				border-radius: 12rpx;
				background-color: #a0def0;
				color: #fff;
				top: 30rpx;
				margin-right: 6px;
				right: 30rpx;
				text-align: center;
				line-height: 45rpx;
			}
	
			.person {
				background-color: #f6c178;
			}
	
			.item-header {
				display: flex;
				align-items: center;
	
				.tag {
					font-size: 24rpx;
					background-color: rgba(0, 0, 0, 0.2);
					color: #fff;
					border-radius: 8rpx;
					margin-left: 12rpx;
					padding: 2rpx 8rpx;
				}
			}
	
			.info {
				padding: 20rpx 0;
				border-bottom: 2rpx solid #ccc;
	
				.text {
					margin: 8rpx 0;
					color: rgba(0, 0, 0, 0.7);
					font-size: 14px;
				}
	
				.labels {
					display: flex;
					flex-wrap: wrap;
	
					.label {
						background-color: rgba(0, 0, 0, 0.2);
						color: #fff;
						border-radius: 8rpx;
						margin-right: 12rpx;
						margin-bottom: 12rpx;
						height: 45rpx;
						font-size: 24rpx;
						text-align: center;
						line-height: 45rpx;
						padding: 0rpx 10rpx;
					}
				}
			}
	
			.item-bottom {
				display: flex;
				align-items: center;
				justify-content: space-between;
				margin-top: 20rpx;
	
				& image {
					width: 48rpx;
					height: 48rpx;
				}
			}
		}
	}

	.add {
		position: fixed;
		z-index: 999;
		right: 40rpx;
		bottom: 200rpx;

		& image {
			width: 160rpx;
			height: 160rpx;
		}
	}
	.title{
		width: 86%;
	}
	.list .item .item-header .tag{    
		float: right;
	width: 30px;
	text-align: center;}
	.search-wrap {
		background-color: #fff;
		padding-right: 30rpx;
		position: absolute;
		right: 0;
		width: 80%;
		z-index: 12;
		box-sizing: border-box;
		.search {
			display: flex;
			align-items: center;
			border: 2rpx solid #ccc;
			padding: 5rpx 20rpx;
			border-radius: 8rpx;
		}

		.icon-search {
			width: 40rpx;
			height: 40rpx;
		}

		.uni-input {
			flex: 1;
			height: 48rpx;
			font-size: 24rpx;
		}
	}

	.dropdown .icon-search {
		right: 28px;
	}

	/deep/ radio-group {
		display: flex;
		justify-content: space-between;
		font-size: 24rpx;
		flex-wrap: wrap;
	}

	/deep/ uni-checkbox-group {
		display: flex;
		font-size: 24rpx;
		justify-content: space-between;
		flex-wrap: wrap;
	}
</style>