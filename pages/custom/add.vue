<template>
	<view class="page">
		<my-navbar title="添加客户" left="left" text="返回"></my-navbar>
		<view class="content">
			<view class="form">
				<view class="form-item">
					<view class="label">客户类型*</view>
					<view class="value">
						<radio-group @change="radioChange">
							<label class="list" v-for="(item, index) in items" :key="item.value">
								<view>
									<radio :value="item.value" checked/>
								</view>
								<view>{{item.name}}</view>
							</label>
						</radio-group>
					</view>
				</view>
				<view class="form-item">
					<view class="label">公司名称*</view>
					<view class="value">
						<input v-model="form.company_name" class="uni-input" placeholder=" " />
					</view>
				</view>
				<view class="form-item">
					<view class="label">法人姓名*</view>
					<view class="value">
						<input v-model="form.name" class="uni-input" placeholder=" " />
					</view>
				</view>
				<view class="form-item">
					<view class="label">法人手机号*</view>
					<view class="value">
						<input v-model="form.mobile" class="uni-input" placeholder=" " />
					</view>
				</view>
				<view class="form-item">
					<view class="label">法人身份证</view>
					<view class="value">
						<input v-model="form.id_num" class="uni-input" type="idcard" placeholder=" "
							@input="changeIdCard()" />
					</view>
				</view>
				<view class="form-item">
					<view class="label">法人生日</view>
					<view class="value">
						<picker mode="date" :value="date" :start="startDate" :end="endDate" @change="bindDateChange">
							<view class="uni-input">{{form.birthday ? form.birthday : '请选择'}}</view>
						</picker>
					</view>
				</view>
				<view class="form-item" v-for="item in labels" :key="item.name">
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
							<label class="list checkbox" v-for="(item1,index) in item.labels">
								<view>
									<checkbox :value="item1" :checked="zhengxin.some(item=>item==item1)||shangwulei.some(item=>item==item1)"/>
								</view>
								<view>{{item1}}</view>
							</label>
						</checkbox-group>
					</view>
				</view>
				<view class="submit" style="margin: 0;">
					<button @click="onSubmit">提交</button>
					<button @click="reset" style="background-color: #acacac;">重置</button>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				items: [{
						name: '企业客户',
						value: "1"
					}//,
					//{
					//name: '个人客户',
					//value: "2"
					//}
				],
				labels: [],
				date: "",
				form: {
					type: 1,
					company_name: "",
					name: "",
					mobile: "",
					id_num: "",
					birthday: "",
					labels: []
				},
				// 开票
				kaipiao:[],
				// 纳税
				nashui:[],
				// 征信
				zhengxin:[],
				// 商务类
				shangwulei:[]
			}
		},
		onLoad() {
			this.getCustomerLabels()
		},
		computed: {
			startDate() {
				return this.getDate('start');
			},
			endDate() {
				return this.getDate('end');
			}
		},
		methods: {
			changeIdCard() {
				if (this.form.id_num.length == 18) {
					const str = this.form.id_num.substr(6, 8)
					this.form.birthday = str.replace(/(.{4})(.{2})/, "$1-$2-");
				}

			},
			reset(){
				this.form.company_name = ''
				this.form.name = ''
				this.form.mobile = ''
				this.form.id_num = ''
				this.form.birthday = ''
				this.kaipiao = []
				this.nashui = []
				this.zhengxin = []
				this.shangwulei = []
			},
			onSubmit() {
				if (!this.form.company_name) {
					uni.showToast({
						title: '请输入公司名称',
						icon: 'none'
					})
					return;
				}

				if (!this.form.name) {
					uni.showToast({
						title: '请输入法人姓名',
						icon: 'none'
					})
					return;
				}

				if (!this.form.mobile) {
					uni.showToast({
						title: '请输入法人手机号',
						icon: 'none'
					})
					return;
				}

				//if (!this.form.id_num) {
				//	uni.showToast({
				//		title: '请输入法人身份证',
				//		icon: 'none'
				//	})
				//	return;
				//}
				this.form.labels = []
				this.form.labels = this.form.labels.concat(this.kaipiao,this.nashui,this.zhengxin,this.shangwulei)
				this.$u.api.customerAdd(this.form).then(res => {
					uni.showToast({
						title: '录入成功',
						duration: 2000,
					});
					setTimeout(() => {
						uni.switchTab({
							url: '/pages/custom/index'
						})
					}, 2000)
				})
			},
			getCustomerLabels() {
				this.$u.api.customerLabels().then(res => {
					this.labels = res
					// consloe
				})
			},
			radioChange(e) {
				console.log(e)
				this.form.type = e.detail.value
			},
			checkboxChange(name,e) {
				console.log(name,e)
				if(name=='开票'){
					this.kaipiao = e.detail.value
				}
				if(name=='纳税'){
					this.nashui = e.detail.value
				}
				if(name=='征信'){
					this.zhengxin = e.detail.value
				}
				if(name=='商务类'){
					this.shangwulei = e.detail.value
				}
			},
			radioChange1(name,e) {
				console.log(name,e)
				if(name=='开票'){
					this.kaipiao = e.detail.value
					console.log(this.kaipiao)
				}
				if(name=='纳税'){
					this.nashui = e.detail.value
				}
				if(name=='征信'){
					this.zhengxin = e.detail.value
				}
				if(name=='商务类'){
					this.shangwulei = e.detail.value
				}
			},
			bindDateChange(e) {
				this.form.birthday = e.detail.value
			},
			getDate(type) {
				const date = new Date();
				let year = date.getFullYear();
				let month = date.getMonth() + 1;
				let day = date.getDate();

				if (type === 'start') {
					year = year - 60;
				} else if (type === 'end') {
					year = year + 2;
				}
				month = month > 9 ? month : '0' + month;
				day = day > 9 ? day : '0' + day;
				return `${year}-${month}-${day}`;
			}
		}
	}
</script>

<style scoped lang="scss">
	.page{
		background: #fff;
	}
	.submit{
		display: flex;
		padding: 20rpx;
		button{
			margin: 10rpx;
		}
	}
	.list {
		display: flex;
		align-items: center;

	}

	.label {
		width: 200rpx;
	}

	.checkbox {
		width: 50%;
		margin: 2px 0;
	}

	/deep/ uni-radio .uni-radio-input {
		width: 32rpx !important;
		height: 32rpx !important;
	}

	/deep/ uni-checkbox .uni-checkbox-input {
		width: 32rpx !important;
		height: 32rpx !important;
	}

	/deep/ radio-group {
		display: flex;
		justify-content: space-between;
		font-size: 24rpx;
		padding-right: 20rpx;
		flex-wrap: wrap;
	}

	/deep/ uni-picker {
		font-size: 28rpx;
	}

	/deep/ uni-checkbox-group {
		display: flex;
		font-size: 24rpx;
		justify-content: space-between;
		padding-right: 20rpx;
		flex-wrap: wrap;
	}
</style>