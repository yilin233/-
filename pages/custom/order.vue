<template>
	<view class="page">
		<my-navbar title="抢单" left="left" text="返回"></my-navbar>
		<view class="content">
			<view class="form">
				<view class="form-item">
					<view class="label">客户名称*</view>
					<view class="value">
						<input class="uni-input" v-model="form.customer_name" type="text" placeholder="请输入客户名称" />
					</view>
				</view>
				<view class="form-item">
					<view class="label">选择合同*</view>
					<view class="value">
						<uni-data-select v-model="form.contract_id" :localdata="contractList" 
							placeholder="请选择合同"></uni-data-select>
					</view>
				</view>
				<view class="form-item">
					<view class="label">合同金额*</view>
					<view class="value">
						<input v-model="form.amount" class="uni-input" type="digit" placeholder="请输入合同金额" style="float: left;"><view style="float: right;color: #909090;">元</view></input>
					</view>
				</view>
				<view class="form-item">
					<view class="label">签约日期*</view>
					<view class="value">
						<uni-datetime-picker type="date" :clear-icon="false" v-model="form.sign_date" />
					</view>
				</view>
				<view class="submit"  @click="onSubmit">
					<button>提交</button>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				form:{
					customer_name:"",
					contract_id:"",
					amount:"",
					sign_date:""
				},
				contractList:[]
			};
		},
		onLoad() {
			this.getContractList()
		},
		methods: {
			getContractList(){
				this.$u.api.contractList({
					keywords: "",
					status: 1
				}).then( res => {
					res.forEach(item => {
						this.contractList.push({
							value:item.id,
							text:item.customer+item.number
						})
					})
				})
			},
			onSubmit(){
				if (!this.form.customer_name) {
					uni.showToast({
						title: '请填写客户名称',
						icon: 'none'
					})
					return;
				}
				if (!this.form.contract_id) {
					uni.showToast({
						title: '请选择合同',
						icon: 'none'
					})
					return;
				}
				
				if (!this.form.amount) {
					uni.showToast({
						title: '请填写合同金额',
						icon: 'none'
					})
					return;
				}
				
				if (!this.form.sign_date) {
					uni.showToast({
						title: '请选择签约日期',
						icon: 'none'
					})
					return;
				}
				
				this.$u.api.loanRobOrder(this.form).then( res => {
					uni.showToast({
						title: '抢单成功',
						duration: 2000,
					});
					setTimeout(() => {
						uni.navigateBack({
							delta: 1
						});
					},2000)
				})
			}
		},
	};
</script>

<style scoped lang="scss">
	.uni-input-placeholder{font-size: 12px;}
	.uni-input {
		padding: 10rpx 20rpx;
		font-size: 14px;
	}
	.page{background: #fff;}
	/deep/ .uni-select {
		border: none !important;
	
	}
	
	/deep/ .uni-date-editor--x {
		border: none !important;
	}
	
	/deep/ .uni-icons{
		margin-left:8rpx !important;
	}
	page{background: #fff;}
</style>