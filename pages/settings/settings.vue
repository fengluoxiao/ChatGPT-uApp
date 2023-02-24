<template>
	
	<view class="u-page">
<view class="tabbar">
			ChatGPT
		</view>
		<view class="" style="padding-top: 100rpx;">
			<u-cell-group>
			    <u-cell>
			    	<view
			    	    slot="title"
			    	    class="u-slot-title"
			    	>
			    	<view  v-if="key!=''" class="" style="display: flex;flex-direction: row;justify-content: space-between;align-items: center">
							<view class="" style="width: 200px;">
							ChatGPT
						</view>
						<image src="../../static/img/e3d955c27d7943b9832e0f35d50f5193.png" mode="aspectFill" style="height: 50px;width: 50px;"></image>
						
			    	</view>
					<view v-else>
						请登录
					</view>
			    	</view>
					
			    </u-cell>
			  
			</u-cell-group>
			<view
			    slot="title"
			    class="u-slot-title"
			>
			<view  v-if="key!=''" class="" style="display: flex;flex-direction: row;justify-content: space-between;align-items: center">
					<view class="" style="width: 200px;">
					ChatGPT
				</view>
				<image src="../../static/img/e3d955c27d7943b9832e0f35d50f5193.png" mode="aspectFill" style="height: 50px;width: 50px;"></image>
				
			</view>
			</view>
			<u-cell-group>
			    <u-cell isLink @click="openSheet(0)">
			    	<view
			    	    slot="title"
			    	    class="u-slot-title"
			    	>
			    	<view  class="" style="display: flex;flex-direction: row;align-items: center">
						<image src="../../static/img/e3d955c27d7943b9832e0f35d50f5193.png" mode="aspectFill" style="height: 50px;width: 50px;"></image>
							<view class="" style="width: 200px;margin-left: 20rpx;">
							设定默认对话模型
						</view>
						<view class="">
							当前选择：{{defaultModel}}
						</view>
						
			    	</view>
					
			    	</view>
					
			    </u-cell>
			  <u-cell isLink @click="openSheet(1)">
			  	<view
			  	    slot="title"
			  	    class="u-slot-title"
			  	>
			  	<view  class="" style="display: flex;flex-direction: row;align-items: center">
			  		<image src="../../static/img/e3d955c27d7943b9832e0f35d50f5193.png" mode="aspectFill" style="height: 50px;width: 50px;"></image>
			  			<view class="" style="width: 200px;margin-left: 20rpx;">
			  			清除chat_key
			  		</view>
			  		
			  	</view>
			  	
			  	</view>
			  	
			  </u-cell>
			</u-cell-group>
			<fui-actionsheet @click="changeDefaultModel" :show="showModelAction" :tips="tips" :itemList="modelAction" maskClosable @cancel="showModelAction = false" zIndex="99999999"></fui-actionsheet>
			<fui-actionsheet @click="clearKey" :show="showModelActionForCLear" :tips="tipsForClear" :itemList="clearAction" maskClosable @cancel="showModelActionForCLear = false" zIndex="99999999"></fui-actionsheet>
			<tui-tabbar :current="current" :unlined="true" :isFixed="true" backdropFilter backgroundColor="rgba(255, 255, 255, 0.7)" :tabBar="tabBar" color="#777" selectedColor="#AC9157" @click="tabbarSwitch"></tui-tabbar>
		</view>
	</view>
</template>
<script>
	import tuiTabbar from "@/components/thorui/tui-tabbar/tui-tabbar.vue"
	import fuiActionsheet from "@/components/firstui/fui-actionsheet/fui-actionsheet.vue"
	export default {
		components:{
				tuiTabbar,
				fuiActionsheet
			},
		data() {
			return {
				showModelAction: false,
				showModelActionForCLear: false,
				tips: '请选择对话模型',
				tipsForClear: '请确认删除',
				modelAction: [
					{text:'text-ada-001'},
					{text:'text-babbage-001'},
					{text:'text-curie-001'},
					{text:'text-davinci-003'},
					],
					clearAction: [
						{text:'是'},
						],
				tabBar: [
					{
						pagePath: '/pages/index/index',
						text: '',
						iconPath: '/static/img/chat-selected.png',
						selectedIconPath: '/static/img/chat-unselected.png'
					},
					{
						pagePath: '/pages/settings/settings',
						text: '',
						selectedIconPath: '/static/img/settings-unselected.png',
						iconPath: '/static/img/settings-selected.png',
						
					},
				],
				show0: false,
				show1: false,
				show2: false,
				show3: false,
				show4: false,
				show5: false,
				show6: false,
				value: 0,
				current: 0,
				key: '',
				defaultModel: '',
				}
		},
		onLoad() {
			
			if(uni.getStorage("chat_key")) {
				this.key = uni.getStorageSync("chat_key")
			} else {
				this.key = ''
			}
		uni.hideTabBar()
		},
		onShow(){
			console.log(uni.getStorageSync('defaultModel').text,"uni.getStorageSync('defaultModel')")
			this.defaultModel = uni.getStorageSync('defaultModel').text
			if(uni.getStorage("chat_key")) {
				this.key = uni.getStorageSync("chat_key")
			} else {
				this.key = ''
			}
		},
		methods: {
			clearKey(e) {
				uni.removeStorageSync('chat_key')
				uni.showToast({
					icon: 'success',
					title: '删除成功'
				})
				this.showModelActionForCLear = false
				this.key = ''
			},
			changeDefaultModel(model) {
				console.log(model,'model')
				this.defaultModel = model.text
				uni.setStorageSync('defaultModel',model)
				this.showModelAction = false
			},
			tabbarSwitch(e) {
				console.log(e,'e')
				uni.switchTab({
					url: e.pagePath
				})
			},
			togglePicker() {
				
			},
			// 点击cell，判断显示哪个功能
			openSheet(index) {
				if(index == 0) {
					this.showModelAction = true
				}
				if(index == 1) {
					this.showModelActionForCLear = true
				}
			},
			getuserinfo(res) {
				uni.$u.toast(`用户名：${res.userInfo.nickName}`)
			},
			navigateBack() {
				uni.navigateBack()
			},
			close() {
				console.log('close');
				this['show0'] = false
			},
			select(e) {
				console.log('select', e);
			},
			
			selectClick(item) {
				console.log(item,'item')
				if(item.id && item.id == 'allClear') {
					uni.clearStorageSync();
					this.key = ''
				}
			}
		}
	}
</script>

<style lang="scss">
	page {
		background-color: #f3f3f3;
	}
	.u-page {
		padding: 0;
	}
	.tabbar {
		height: 100rpx;
		line-height: 100rpx;
		text-align: center;
		position: fixed;
		display: flex;
		justify-content: center;
		width: 100%;
		background: rgba(255, 255, 255, 0.7);
		backdrop-filter: blur(20px);
		z-index: 9999999;
	}
	image {
		width: 40px;
	}
</style>