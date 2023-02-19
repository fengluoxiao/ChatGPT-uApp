<template>
	<view class="container" @tap="togglePicker(0)">
		<!--内容-->
		<view class="tabbar">
			ChatGPT
		</view>
		<view class=""  v-if="isLogin == true" style="position: relative;top: 120rpx;" :style="{height: scrollHeight}">
			<view class="scroll" >
					<scroll-view :scroll-into-view="scrollViewId" scroll-y style="height: 2000rpx;padding-bottom: 210rpx;">
						<view class="item-space"></view>
						<view v-for="(item, index) in list" :key="index" >
							<!--撤销-->
							<view class="item flex-row" :class="[item.source == fromUserId ? 'right' : 'left']">
								<!--处理头像-->
								<view>
									<image  src="@/static/img/e3d955c27d7943b9832e0f35d50f5193.png" class="face"></image>
								</view>
								<view >
									<image v-if="item.toUserFace || item.userFace" src="item.toUserFace || item.userFace" class="face"></image>
									<image v-else src="" class="face"></image>
								</view>
								<!--文本-->
								<view v-if="item.msgType == 'text'" @longpress="longPress(item)" class="content flex-row">{{ item.content }}</view>
								<!--图片-->
						
								
							</view>
						</view>
						<view id="bottom" style="margin-bottom: 520rpx;"></view>
						
					</scroll-view>
				</view>
			</view>
			<view class="" v-if="isLogin == false">
				<view class="sssss" style="margin-top: 50vh;display: flex;justify-content: center;">
					<view class="">
						<tui-button width="220rpx" @click="toLogin">登录</tui-button>
					</view>
				</view>
			</view>
			<view class="oper flex-row" @tap.prevent.stop="" >
				<view class="" style="display: flex;justify-content: center;padding: 0 100rpx;width: 100%;">
					 <u-icon name="list-dot" color="#000" size="32" @click="openDrawer"></u-icon>
					<input v-if="isEdit" @focus="inputFcus" :focus="isFocus" :cursor-spacing="8" :adjust-position="false" type="text" v-model="content" class="input" placeholder="请输入内容"/>
					<!--发送-->
					<view @touchend.prevent="send"  class="btn">发送</view>
				</view>
				
			</view>
			<tui-tabbar :current="current" :unlined="true"  :isFixed="true" backdropFilter backgroundColor="rgba(255, 255, 255, 0.7)" :tabBar="tabBar" color="#777" selectedColor="#AC9157" @click="tabbarSwitch"></tui-tabbar>
	<!-- 		<tui-bottom-popup mask :zIndex="99999999" :maskZIndex="9999999" :show="popupShow" @close="popup" :height="500">
				
				<view style="height: 500rpx;padding: 20rpx;">
				
				   <tui-dropdown-list :show="dropdownShow" :top="94" >
				    	<template v-slot:selectionbox>
				    		<view class="" style="padding: 20rpx;">
								<tui-row marginBottom="10px" :isFlex="true" justify="space-around" :gutter ="0">
									<tui-col>
										<view class="tui-col__demo tui-yellow">ssss</view>
									</tui-col>
									<tui-col>
										<view class="" @click.stop="openSelect" style="float: right;">
											{{model}}bb
										</view>
									</tui-col>
								</tui-row>
				    			
				    		</view>
				    	</template>
				    	<template v-slot:dropdownbox style="">
				    		<view class="" style="">
								<tui-row marginBottom="10px" :isFlex="true" justify="space-around" :gutter ="0">
									<tui-col>
										
									</tui-col>
									<tui-col>
										<view class="" style="position: absolute;right: 0;">
											
												sasapshsasasass
										
										</view>
									</tui-col>
								</tui-row>
				    			
				    		</view>
				    	</template>
				    </tui-dropdown-list>
				</view>
			</tui-bottom-popup> -->
			<view>
				<fui-bottom-popup :show="popupShow" @close="closePopup" zIndex="9999999999" maskClosable>
					<view class="fui-custom__wrap" style="height: 500rpx;display: flex;padding: 20rpx;flex-direction: column;">
						<tui-dropdown-list :show="dropdownShowForModel" :top="70" :height="500">
						 	<template v-slot:selectionbox>
						 		<view class="" >
									<tui-row marginBottom="10px" :isFlex="true" justify="space-around" :gutter ="0">
										<tui-col>
											<view class="tui-col__demo tui-yellow">已选择的模型：{{model}}</view>
										</tui-col>
										<tui-col>
											<view class="" @click="openSelect(0)" style="float: right;padding: 10rpx 40rpx;border: 1px solid #eee;text-align: center;">
												{{model}}
											</view>
										</tui-col>
									</tui-row>
						 			
						 		</view>
						 	</template>
						 	<template v-slot:dropdownbox style="">
						 		<view class="" style="">
									<tui-row marginBottom="10px" :isFlex="true" justify="space-around" :gutter ="30">
										<tui-col>
											
										</tui-col>
										<tui-col>
											<view class="" style="float: right;border: #eee 1px solid;background-color: #fff;" v-for="(item,index) in choModel" >
													<view class="" style="border: #eee 1px solid;width: 300rpx;display: flex;justify-content: center;">
														<view class="" @click.stop="changeModel(index)">
															{{item}}
														</view>
													</view>
											</view>
										</tui-col>
									</tui-row>
		
						 		</view>
						 	</template>
						 </tui-dropdown-list>
				<tui-dropdown-list :show="dropdownShowForToken" :top="94" :height="500">
					<template v-slot:selectionbox>
						<view class="" >
							<tui-row marginBottom="10px" :isFlex="true" justify="space-around" :gutter ="0">
								<tui-col>
									<view class="tui-col__demo tui-yellow">ssss</view>
								</tui-col>
								<tui-col>
									<view class="" @click="openSelect(1)" style="float: right;padding: 10rpx 40rpx;border: 1px solid #eee;text-align: center;">
										{{model}}bb
									</view>
								</tui-col>
							</tui-row>
							
						</view>
					</template>
					<template v-slot:dropdownbox style="">
						<view class="" style="">
						<tui-row marginBottom="10px" :isFlex="true" justify="space-around" :gutter ="30">
							<tui-col>
								
							</tui-col>
							<tui-col>
								<view class="" style="position: absolute;right: 0;">
									
										sasapshsasasass
								
								</view>
							</tui-col>
						</tui-row>
								
							</view>
						</template>
					 </tui-dropdown-list>
					</view>
				</fui-bottom-popup>
			<!-- 		<u-popup :show="popupShow" @close="close" :customStyle="{height: '200rpx'}" :zIndex="9999999999999" safeAreaInsetBottom round="20">
			            <view style="height: 500rpx;padding: 30rpx;">
						
			               <tui-dropdown-list :show="dropdownShow" :top="94" :height="500">
			                	<template v-slot:selectionbox>
			                		<view class="" >
										<tui-row marginBottom="10px" :isFlex="true" justify="space-around" :gutter ="0">
											<tui-col>
												<view class="tui-col__demo tui-yellow">ssss</view>
											</tui-col>
											<tui-col>
												<view class="" @click="openSelect" style="float: right;padding: 10rpx 40rpx;border: 1px solid #eee;text-align: center;">
													{{model}}bb
												</view>
											</tui-col>
										</tui-row>
			                			
			                		</view>
			                	</template>
			                	<template v-slot:dropdownbox style="">
			                		<view class="" style="">
										<tui-row marginBottom="10px" :isFlex="true" justify="space-around" :gutter ="30">
											<tui-col>
												
											</tui-col>
											<tui-col>
												<view class="" style="position: absolute;right: 0;">
													
														sasapshsasasass
												
												</view>
											</tui-col>
										</tui-row>
			                			
			                		</view>
			                	</template>
			                </tui-dropdown-list>
							<tui-dropdown-list :show="dropdownShow" :top="94" :height="500">
							 	<template v-slot:selectionbox>
							 		<view class="" >
										<tui-row marginBottom="10px" :isFlex="true" justify="space-around" :gutter ="0">
											<tui-col>
												<view class="tui-col__demo tui-yellow">ssss</view>
											</tui-col>
											<tui-col>
												<view class="" @click="openSelect" style="float: right;padding: 10rpx 40rpx;border: 1px solid #eee;text-align: center;">
													{{model}}bb
												</view>
											</tui-col>
										</tui-row>
							 			
							 		</view>
							 	</template>
							 	<template v-slot:dropdownbox style="">
							 		<view class="" style="">
																	<tui-row marginBottom="10px" :isFlex="true" justify="space-around" :gutter ="30">
																		<tui-col>
																			
																		</tui-col>
																		<tui-col>
																			<view class="" style="position: absolute;right: 0;">
																				
																					sasapshsasasass
																			
																			</view>
																		</tui-col>
																	</tui-row>
							 			
							 		</view>
							 	</template>
							 </tui-dropdown-list>
			            </view>
					</u-popup> -->
					<u-button @click="show = true">打开</u-button>
				</view>
		</view>
	

</template>

<script>
import tuiButton from "@/components/thorui/tui-button/tui-button.vue"
import tuiTabbar from "@/components/thorui/tui-tabbar/tui-tabbar.vue"
import tuiDrawer from "@/components/thorui/tui-drawer/tui-drawer.vue"
import tuiDropdownList from "@/components/thorui/tui-dropdown-list/tui-dropdown-list.vue"
import tuiBottomPopup from "@/components/thorui/tui-bottom-popup/tui-bottom-popup.vue"
import tuiCol from "@/components/thorui/tui-col/tui-col.vue"
import tuiRow from "@/components/thorui/tui-row/tui-row.vue"
import fuiBottomPopup from "@/components/firstui/fui-bottom-popup/fui-bottom-popup.vue"
export default{
	
	data(){
		
		return {
			popup: 'height: 500rpx',
			tabBar: [
				{
					pagePath: '/pages/index/index',
					text: '',
					iconPath: '/static/img/chat-unselected.png',
					selectedIconPath: '/static/img/chat-selected.png'
				},
				{
					pagePath: '/pages/settings/settings',
					text: '',
					selectedIconPath: '/static/img/settings-selected.png',
					iconPath: '/static/img/settings-unselected.png',
					
				},
			],
			isEdit: true,
			isFocus: false,
			scrollHeight: 'auto',		// 内容区域高度
			statusBarHeight: 0,		// 状态栏高度
			scrollViewId: '',		// 滚动到最底部
			voicePlayingId: '',		// 正在播放的消息ID
			content: '',
			list: [],
			socketMsgQueue: [],
			fromUserId: uni.getStorageSync('userId'),
			fromUserFace: uni.getStorageSync('userFace'),
			toUserId: '',
			toUserName: '',
			isLogin: false,
			value: 0,
			current: 0,
			visible: false,
			model: 'text-davinci-003',
			choModel: ['text-ada-001','text-babbage-001','text-curie-001','text-davinci-003'],
			dropdownShowForModel: false,
			dropdownShowForToken: false,
			popupShow: false
			
		}
	},
	components:{
			tuiButton,
			tuiTabbar,
			tuiDrawer,
			tuiDropdownList,
			tuiBottomPopup,
			tuiCol,
			tuiRow,
			fuiBottomPopup
		},
	onLoad(option){
		// 初始化内容高度
		this.setScrollHeight()
		uni.hideTabBar()
		// 初始化状态栏高度
		uni.getSystemInfo({
			success: res=>{
				this.statusBarHeight = res.statusBarHeight
			}
		});
		if(uni.getStorageSync('chat_key')) {
			this.isLogin = true
		} else {
			this.isLogin = false
		}
		if(uni.getStorageSync("defaultModel")) {
			this.model = uni.getStorageSync("defaultModel").text
		}
	},
	onShow() {
		if(uni.getStorageSync('chat_key')) {
			this.isLogin = true
		} else {
			this.isLogin = false
		}
		if(uni.getStorageSync("defaultModel")) {
			this.model = uni.getStorageSync("defaultModel").text
		}
	},
	onHide(){
		
	},
	onBackPress(){
		if(this.showFile || this.showEmoji){
			this.showFile = false
			this.showEmoji = false
			this.setScrollHeight(0)
			return true
		}
		return false
	},
	methods: {
		changeModel(index) {
			console.log(index,'changeModel')
			console.log(this.choModel[index],'hhh')
			this.model = this.choModel[index]
			this.dropdownShowForModel = false
		},
		openSelect(index) {
			if(index == 0) {
				this.dropdownShowForModel = !this.dropdownShowForModel
				this.dropdownShowForToken = false
			}
			if(index == 1) {
				this.dropdownShowForToken = !this.dropdownShowForToken
				this.dropdownShowForModel = false
			}
		},
		openDrawer() {
			this.popupShow = !this.popupShow
		},
		closePopup() {
			this.popupShow = false
			this.dropdownShowForToken = false
			this.dropdownShowForModel = false
		},
		tabbarSwitch(e) {
			console.log(e,'e')
			uni.switchTab({
				url: e.pagePath
			})
		},
		togglePicker() {
			
		},
		toLogin() {
			uni.navigateTo({
				url: '/pages/login/login'
			})
		},
		// 初始化滚动
		initScrollBar(){
			setTimeout(()=>{
				this.scrollViewId = ''
				setTimeout(()=>{
					this.scrollViewId = 'bottom'
					setTimeout(()=>{this.scrollViewId = ''}, 100)
				}, 100)
			}, 100)
		},
		// 监听输入聚焦
		inputFocus(e){
			this.setScrollHeight(e.detail.height)
			this.initScrollBar()
			
			uni.onKeyboardHeightChange(res=>{
				this.setScrollHeight(res.height)
				this.initScrollBar()
			})
		},
		// 设置scroll的高度
		setScrollHeight(descHeight=0){
			// #ifdef MP-WEIXIN
			this.scrollHeight = `calc(100vh - 110rpx - ${descHeight}px)`
			// #endif
			// #ifdef APP-PLUS
			this.scrollHeight = `calc(100vh - 110upx - ${descHeight}px)`
			// #endif
			// #ifdef H5
			this.scrollHeight = `calc(100vh - 110upx - 88rpx - ${descHeight}px)`
			// #endif
		},
		// 发送消息
		send(){
		this.initScrollBar()

			this.pushMessage(this.content, 'text', ()=>{
				this.content = ''
			})
			
		},
		// 推送消息
		pushMessage(content, type='text', cb=()=>{}){
			if(content == '') {
				uni.showToast({
					icon:'error',
					title: '输入为空'
				})
			} else {
				let msgData = {
					fromUserId: this.fromUserId,
					userFace: uni.getStorageSync('userFace'),
					toUserId: this.toUserId,
					type,
					content
				}
				
				// 加入信息
				this.list.push({
					source: this.fromUserId,
					target: this.toUserId,
					content: msgData.content,
					userFace: uni.getStorageSync('userFace'),
					type: 'single',
					msgType: type
				})
				
				// 初始化滚动条
				this.initScrollBar()
				cb ? cb() : this.togglePicker(0, 'file')
				
				var apiUrl = 'https://api.openai.com/v1/completions';
				
				uni.request({
					url: apiUrl,
					header: {
						// 'Authorization':'Bearer' + ' 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'
						'Authorization':'Bearer ' + uni.getStorageSync('chat_key')
					},
					method:'POST',
					data: {
						"model": this.model,
						"prompt": content,
						  "max_tokens": 2500,
					},
					success: (res) => {
						console.log('content:', content);
						console.log('request success:', res.data.choices[0].text);
						// 加入信息
						this.list.push({
							source: this.fromUserId+1,
							target: this.toUserId,
							content: res.data.choices[0].text,
							userFace: uni.getStorageSync('userFace'),
							type: 'single',
							msgType: type
						})
						
						// 初始化滚动条
						this.initScrollBar()
						cb ? cb() : this.togglePicker(0, 'file')
					},
					fail: (err) => {
						console.log('request fail', err);
						uni.showModal({
							content: err.errMsg,
							showCancel: false
						})
					}
				});
			}
			// 组合消息体：需要保存在本地数据库的数据
			
		},
		// 获取记录
		getList(){
			setTimeout(()=>{
				this.scrollViewId = 'bottom'
				setTimeout(()=>{this.scrollViewId = ''}, 100)
			}, 100)
		
		}
	}
}
</script>

<style lang="scss" scoped>
	page {
		background-color: #eee;
	}
	uni-view {
		display: inline;
	}
	.popup {
		height:500rpx
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
	.flex-row{
		display: flex;
		flex-direction: row;
		align-items: center;
		justify-content: space-between;
	}
.container{
	overflow: hidden;

}

/* #ifdef H5 */
.container{
	height: calc(100vh - 88upx);
}
/* #endif */

.status_bar,
.container,
.header,
.emoji,
.file{
	background-color: #F3F3F3;
}
.header{
	
	border-bottom: 2upx solid #EEE;
	
	.center{
		font-weight: bold;
	}
}

.oper{
	height: 140upx;
	padding: 0 60rpx;
	box-sizing: border-box;
	display: flex;
	justify-content: space-around;
	position: fixed;
	bottom: 90rpx;
	width: 100%;
	background: rgba(255, 255, 255, 0.7);
	backdrop-filter: blur(20px);
	
	.input{
		height: 88rpx;
		line-height: 88upx;
		padding: 0 20rpx;
		font-size: 28upx;
		border: 2rpx solid #EEE;
		border-radius: 20rpx;
		width: 320rpx;
		margin: 0 40rpx;
	}
	.icon{
		width: 60upx;
		height: 60upx;
	}
	.btn{
		color: #fff;
		width: 140upx;
		height: 88upx;
		font-size: 24upx;
		line-height: 88upx;
		text-align: center;
		border-radius: 10upx;
		background-color: #2BA245;
	}
}
.scroll{
	overflow-y: auto;
	transition: all 0.1s ease;
	height: calc(100vh - 88upx - 110upx - var(--status-bar-height));
	padding-top: 100rpx;
	width: 100%;
	/* #ifdef MP-WEIXIN */
	height: calc(100vh - 88upx - var(--status-bar-height));
	/* #endif */
	/* #ifdef H5 */
	height: calc(100vh - 88upx - 110upx - var(--status-bar-height));
	/* #endif */
	
	.item-space{
		height: 30upx;
	}
	
	.time{
		color: #666;
		font-size: 24upx;
		text-align: center;
		margin-bottom: 20upx;
	}
	
	.cancel{
		width: 100%;
		height: 40upx;
		text-align: center;
		margin-bottom: 30upx;
		
		.text{
			color: #999;
			font-size: 24upx;
		}
	}
	
	.item{
		margin: 0 30upx 30upx;
		align-items: flex-start;
		justify-content: flex-start;
		
		.face{
			width: 80upx;
			height: 80upx;
			border-radius: 10upx;
		}
		
		.content{
			color: #000;
			font-size: 32upx;
			// min-height: 80upx;
			border-radius: 10upx;
			padding: 20upx 24upx;
			background-color: #fff;
			word-break: break-all;
			word-wrap: break-word;
			max-width: calc(100% - 100upx - 120upx);
			position: relative;
			margin-left: -100rpx;
			&::before{
				content: '';
				width: 0;
				height: 0;
				border-right: 30upx solid #FFF;
				border-top: 20upx solid transparent;
				border-bottom: 20upx solid transparent;
				position: absolute;
				top: 24upx;
			}
			
			.voice-icon{
				width: 32upx;
				height: 40upx;
				margin-right: 180upx;
				margin-bottom: -8upx;
			}
		}
		
		&.left{
			.face{
				margin-right: 30upx;
			}
			.content::before{
				left: -20upx;
			}
		}
		
		&.right{
			flex-direction: row-reverse;
			.face{
				
			}
			.content{
				background-color: #A0EA6F;
				margin-right: -50rpx;
				&::before{
					right: -20upx;
					transform: rotate(180deg);
					border-right: 30upx solid #A0EA6F;
				}
				
				.voice-icon{
					margin-right: 0;
					margin-left: 180upx;
					transform: rotate(180deg);
				}
			}
		}
	}
	
	#bottom{
		height: 0;
	}
}
</style>
