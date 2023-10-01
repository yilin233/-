<template>
	<view class="page">
		<my-navbar title="客户"></my-navbar>
		<view class="content">
			<view class="dropdown">
				<u-dropdown :close-on-click-mask="true" :borderBottom="false"  ref="uDropdown">
					<u-dropdown-item @change="dropdownChange" v-model="form.range" title="客户类型"
						:options="options1"></u-dropdown-item>
					<u-dropdown-item @change="dropdownChange" v-model="form.order" title="排序"
						:options="options2"></u-dropdown-item>
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
				<view class="icon-search" @click="searchByKeywords">
					<image src="/static/images/icon-search.png"></image>
				</view>
			</view>
			<view class="add" @click="add">
				<image src="/static/images/icon-add.png"></image>
			</view>
			<view class="list">
				<view class="item" v-if="" v-for="(item,index) in list" :key="index">
					<view class="item-header">
						<view class="title">{{item.company_name}}</view>
						<view class="tag" :style="{backgroundColor: item.protect_text.color}">{{item.protect_text.text}}
						</view>
					</view>
					<view class="info">
						<view class="text">法人：{{item.name}}</view>
						<view class="text">联系电话：{{item.mobile}}
						<view style="float: right;">到访次数：{{item.order_number}}</view></view>
						<view class="text">加入日期：{{item.add_date}}
							<view style="float: right;">{{item.surplus_days}}</view>
						</view>
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
		<u-popup v-model="show" mode="center">
			<view class="popup-content">
				<view class="search-popup">
					<input placeholder="请输入关键词" v-model="form.keywords"></input>
					<image src="/static/images/icon-search.png" @click="searchConfirm"></image>
				</view>
			</view>
		</u-popup>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				options1: [{
						label: '全部客户',
						value: 0,
					},
					{
						label: '临时保护客户',
						value: 1,
					},
					{
						label: '永久保护客户',
						value: 2,
					},
					{
						label: '下属的客户',
						value: 3,
					}
				],
				options2: [{
						label: '剩余天数升序',
						value: 1,
					},
					{
						label: '剩余天数降序',
						value: 2,
					},
				],
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
			searchConfirm() {
				this.form.page = 1;
				this.list = [];
				this.getList()
				this.show = false;
			},
			searchByKeywords() {
				this.show = true;
			},
			call(item) {
				uni.makePhoneCall({
					phoneNumber: item.mobile
				});
			},
			getList() {
				this.$u.api.myCustomer(this.form).then(res => {
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
			add() {
				uni.navigateTo({
					url: '/pages/custom/add'
				});
			},
			showMenu(item) {
				const that = this
				uni.showActionSheet({
					itemList: ['移除我的保护库'],
					success: function(res) {
						if (res.tapIndex == 0) {
							that.onRemove(item)
						}
						console.log('选中了第' + (res.tapIndex + 1) + '个按钮');
					},
					fail: function(res) {
						console.log(res.errMsg);
					}
				});
			},
			onRemove(item) {
				console.log(item)
				this.$u.api.customerRemove({
					customer_id: item.id
				}).then(res => {
					uni.showToast({
						title: '移除成功',
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
		}
	}
</script>

<style scoped lang="scss">
	.aa {
		transform: translateY(0px);
	}

	.bb {
		-webkit-transform: translate3D(0, -100%, 0);
		transform: translate3D(0, -100%, 0);
	}

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
		width: 30%;
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
			padding:10rpx;
			.btn {
				width: 200px;
				height: 34px;
				line-height: 34px;
				text-align: center;
				background-color: #5595df;
				color: #fff;
				font-size: 14px;
				border: none;
				margin: 10rpx;
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

	.popup-content {
		padding: 20rpx;

		.search-popup {
			display: flex;
			border-radius: 16rpx;

			& input {
				width: 400rpx;
				height: 44rpx;
			}

			& image {
				width: 48rpx;
				height: 48rpx;
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

	.list .item .info .text {
		font-size: 14px;
	}
	.title{
		width: 86%;
	}
	.list .item .item-header .tag{    
		float: right;
    width: 30px;
    text-align: center;}
	.list .item .info{
    padding: 10px 0;
    border-bottom: 1px solid #ccc;
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