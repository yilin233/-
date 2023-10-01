<template>
	<view class="page">
		<my-navbar title="到访" left="left" text="返回" rightText="我的到访" @clickRight="goList" ></my-navbar>
		<view class="content">
			<view class="form">
				<view class="form-item">
					<view class="label">选择方式*</view>
					<view class="value">
						<radio-group @change="radioChange">
							<label class="list" v-for="(item, index) in items" :key="item.value">
								<view>
									<radio :value="item.value" :checked="type == item.value"/>
								</view>
								<view>{{item.name}}</view>
							</label>
						</radio-group>
					</view>
				</view>
				<view v-if="type == 1" class="form-item">
					<view class="label">选择客户*</view>
					<view class="value">
						<uni-data-select v-model="form.customer_id" @change="customChange" :localdata="customList" placeholder="请选择客户"></uni-data-select>
					</view>
				</view>
				<view v-if="type == 2" class="form-item">
					<view class="label">客户名称*</view>
					<view class="value">
						<input class="uni-input" v-model="form.name" placeholder="请输入客户" />
					</view>
				</view>
				
				<view class="form-item">
					<view class="label">到访时间*</view>
					<view class="value">
						<uni-datetime-picker type="datetime" :clear-icon="false" v-model="form.visit_time" />
					</view>
				</view>
				<view class="form-item">
					<view class="label">备注内容*</view>
					<view class="value">
						<textarea class="uni-input" v-model="form.content" placeholder="请输入内容" /></textarea> 
					</view>
				</view>
				
				
				<view class="form-item" v-for="item in labels" :key="item.name">
					<view class="label">{{item.name}}</view>
					<view class="value">
						<radio-group @change="labelChange(item,$event)" v-if="item.select_type==1">
							<label class="list checkbox" v-for="c in item.labels" :key="c">
								<view>
									<radio :value="c" :checked="isCheck(item,c)" />
								</view>
								<view>{{c}}</view>
							</label>
						</radio-group>
						<checkbox-group @change="labelChange(item,$event)" v-if="item.select_type==2">
							<label class="list checkbox" :key="c" v-for="(c,index) in item.labels">
								<view>
									<checkbox :value="c" :checked="isCheck(item,c)" />
								</view>
								<view>{{c}}</view>
							</label>
						</checkbox-group>
					</view>
				</view>
				
				
				<view class="submit"  style="justify-content: center;" >
					<button  @click="onSubmit">提交</button>
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
				type: '1',
				items: [{
						name: '手动选择客户',
						value: "1"
					} ,
					{
					name: '手动输入客户',
					value: "2"
					}
				],
				form:{
					name:"",
					customer_id:"",
					visit_time:"",
					content:"",
					labels: []
				},
				labels: [],
				customList:[],
				
			};
		},
		onLoad() {
			this.getCustom()
			
		},
		watch:{
			type(){
				this.form.labels = [];
				this.form.name = this.form.customer_id = ''
			}
		},
		methods: {
			radioChange({detail:{value}}){
				this.type = value;
			},
			customChange(v){
				this.form.labels = (this.customList.find(c=> c.value == v) || {labels: []}).labels;
				console.log( this.form.labels,v,this.customList )
			},
			reset(){
				this.form.customer_id = ''
				this.form.name = '';
				this.form.visit_time = ''
				this.form.content = ''
				this.form.labels = [];
			},
			goList(){
				uni.navigateTo({
					url: '/pages/custom/visitlog'
				})
			},
			getCustom(){
				this.$u.api.customerLabels().then(res => {
					this.labels = res
				})
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
							...item,
							value:item.id,
							text:item.company_name
						})
					})
				})
			},
			isCheck(item,c){
				return  this.form.labels.some(v=> v === c)
			},
			labelChange(item,{detail:{value:v}}){
				const arr = this.form.labels;
				
				
				let isPush = 1;
				for(let i = 0; i < arr.length; i++){
					if( !item.labels.some(c=> c=== arr[i]) ) continue;
					arr.splice(i,1);
					if(arr[i] === v && item.select_type == 1) isPush = 0;
					i--;
				}
				isPush && (item.select_type == 1 ? arr.push(v) : arr.push(...v));
				console.log(this.form.labels,item.select_type,v)
				
			},
			 
			onSubmit(){
				if (this.type == 1 && !this.form.customer_id) {
					uni.showToast({
						title: '请选择客户',
						icon: 'none'
					})
					return;
				}
				if (this.type == 2 && !this.form.name) {
					uni.showToast({
						title: '请输入客户名称',
						icon: 'none'
					})
					return;
				}
				if (!this.form.visit_time) {
					uni.showToast({
						title: '请选择到访时间',
						icon: 'none'
					})
					return;
				}
				if (!this.form.content) {
					uni.showToast({
						title: '请填写备注内容',
						icon: 'none'
					})
					return;
				}
				this.$u.api.customerVisit(this.form).then( res => {
					uni.showToast({
						title: '添加成功',
						duration: 2000,
					});
					setTimeout(() => {
						uni.switchTab({
							url: '/pages/index/index'
						})
					}, 2000)
				})
			}
		},
	};
</script>

<style scoped lang="scss">
	.uni-input-placeholder{font-size: 12px;}
	.uni-textarea-placeholder{font-size: 12px;}
	/deep/ .uni-select__selector-item{font-size: 12px !important;text-align: left;}
	uni-textarea{width: 225px;height: 50px;}
	/deep/ .uni-textarea-textarea{font-size: 12px;}
	.uni-input {
		padding: 0 20rpx;
	}
	/deep/ .uni-date__x-input{font-size: 12px;}
	/deep/ .uni-input-input{font-size: 12px;}
	.uni-textarea {
		padding: 0 20rpx;
	}

	/deep/ .uni-select {
		border: none !important;

	}

	/deep/ .uni-date-editor--x {
		border: none !important;
	}
	
	/deep/ .uni-icons{
		margin-left:8rpx !important;
	}
	
	
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