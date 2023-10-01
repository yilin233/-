<template>
	<view class="page">
		<my-navbar title="签约" left="left" text="返回"></my-navbar>
		<view class="content">
			<view class="form">
				<view class="form-item">
					<view class="label">选择客户*</view>
					<view class="value">
						<uni-data-select v-model="form.customer_id" :localdata="customList" placeholder="请选择客户"></uni-data-select>
					</view>
				</view>
				<view class="form-item">
					<view class="label">选择合同*</view>
					<view class="value">
						<uni-data-select v-model="form.contract_id" :localdata="contractList" placeholder="请选择合同"></uni-data-select>
					</view>
				</view>
				<view class="form-item">
					<view class="label">合同金额*</view>
					<view class="value">
						<input v-model="form.amount" class="uni-input" type="digit" placeholder="请输入合同金额" style="float: left;"><view style="float: right;color: #909090;">元</view></input>
					</view>
				</view>
				<view class="form-item">
					<view class="label">金融专员*</view>
					<view class="value">
						<uni-data-select v-model="form.commissioner_id" :localdata="commissionerList" placeholder="请选择金融专员"></uni-data-select>
					</view>
				</view>
				<view class="form-item">
					<view class="label">签约日期*</view>
					<view class="value">
						<uni-datetime-picker type="date" :clear-icon="false" v-model="form.sign_date" />
					</view>
				</view>
				<view class="submit" @click="onSubmit">
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
				contractList:[],
				customList:[],
				commissionerList:[],
				form:{
					customer_id:"",
					commissioner_id:"",
					contract_id:"",
					amount:"",
					sign_date:""
				}
			};
		},
		onLoad() {
			this.getCustom()
			this.getCommissionerList()
			this.getContractList()
		},
		methods: {
			getContractList(){
				this.$u.api.contractList({
					keywords: "",
					status: 1
				}).then( res => {
					console.log("12",res)
					res.forEach(item => {
						this.contractList.push({
							value:item.id,
							text:item.customer+item.number
						})
					})
					console.log(this.contractList)
				})
			},
			getCustom(){
				this.$u.api.myCustomer({
					type: 0,
					range: "",
					order: 0,
					keywords:"", 
					page: 1,
					page_size: 100
				}).then( res => {
					res.forEach(item => {
						this.customList.push({
							value:item.id,
							text:item.company_name
						})
					})
				})
			},
			getCommissionerList(){
				this.$u.api.commissionerList().then( res => {
					res.forEach(item => {
						this.commissionerList.push({
							value:item.id,
							text:item.name
						})
					})
				})
			},
			onSubmit(){
				if (!this.form.customer_id) {
					uni.showToast({
						title: '请选择客户',
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
						title: '请输入合同金额',
						icon: 'none'
					})
					return;
				}
				if (!this.form.commissioner_id) {
					uni.showToast({
						title: '请选择金融专员',
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
				this.$u.api.loanSign(this.form).then( res => {
					uni.showToast({
						title: '签约成功',
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
		padding: 0 20rpx;
	}
	/deep/ .uni-select__selector-item{text-align: left;}
	/deep/ .uni-select {
		border: none !important;
	
	}
	
	/deep/ .uni-date-editor--x {
		border: none !important;
	}
	
	/deep/ .uni-icons{
		margin-left:8rpx !important;
	}
</style>