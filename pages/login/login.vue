<template>
	<view style="padding: 20rpx;">
		<view class="" style="margin-bottom: 20rpx;">
			输入您的Key
		</view>
		 <u--input
		 :disabled="disabled"
		    placeholder="请输入内容"
		    border="bottom"
		    clearable
			@change="change"
		  ></u--input>
		  <view class="" style="margin-top: 20rpx;">
		  	<u-button @click="toLogin" type="primary" text="登录" :disabled="disabled"></u-button>
		  </view>
		  
	</view>
</template>

<script>
	export default {
		data() {
			return {
				disabled: false
			}
		},
		methods: {
			change(e) {
				console.log(e)
				this.value = e
			},
			toLogin() {
				if(this.value) {
					console.log("key是：",this.value)
					uni.setStorageSync("chat_key",this.value)
							this.disabled = true
							uni.showToast({
								icon:'success',
								title: "登录成功"
							})
							setTimeout(function() {
								uni.switchTab({
									url: '/pages/index/index'
								})
							},2000)
				} else {
					uni.showToast({
						icon:'error',
						title:"输入为空"
					})
				}
			}
		}
	}
</script>

<style>

</style>
