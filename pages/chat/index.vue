<template>
	<view class="container" @tap="togglePicker(0)">
		<!--内容-->
		<view class="tabbar">
			ChatGPT
		</view>
		<view class="scroll" :style="{height: scrollHeight}">
			<scroll-view :scroll-into-view="scrollViewId" scroll-y style="height: 1000px;">
				<view class="item-space"></view>
				<!-- <view class="time">晚上 10:04</view> -->
				<view v-for="(item, index) in list" :key="index" >
					<!--撤销-->
					<view v-if="item.msgType == 'cancel'" class="cancel">
						<text v-if="item.source == fromUserId" class="text">你撤回了一条消息</text>
						<text v-else class="text">成员 {{ item.source }} 撤回了一条消息</text>
					</view>
					<view v-else class="item flex-row" :class="[item.source == fromUserId ? 'right' : 'left']">
						<!--处理头像-->
						<view v-if="item.source == fromUserId">
							<image v-if="fromUserFace" :src="fromUserFace" class="face"></image>
							<image v-else src="" class="face"></image>
						</view>
						<view v-else>
							<image v-if="item.toUserFace || item.userFace" :src="item.toUserFace || item.userFace" class="face"></image>
							<image v-else src="" class="face"></image>
						</view>
						<!--文本-->
						<view v-if="item.msgType == 'text'" @longpress="longPress(item)" class="content flex-row">{{ item.content }}</view>
						<!--图片-->
						<image v-if="item.msgType == 'image'" @longpress="longPress(item)" :src="item.content" mode="widthFix" style="width: 320upx;"></image>
						<!--地图-->
						<view v-if="item.msgType == 'map'" class="content map" @tap="openLocation(item.content)">
							<view class="title">{{ item.content.address }}</view>
							<map class="box"
								:latitude="item.content.latitude" 
								:longitude="item.content.longitude"
								:markers="[{latitude: item.content.latitude, longitude: item.content.longitude, iconPath: ''}]" 
								@tap="openLocation(item.content)"
							></map>
						</view>
						<!--语音-->
						<view v-if="item.msgType == 'voice'" @tap="playVoice(item)" class="content">
							<image v-show="voicePlayingId != item.id" src="" class="voice-icon"></image>
							<image v-show="voicePlayingId == item.id" src="" class="voice-icon"></image>
						</view>
					</view>
				</view>
				<view id="bottom" style="padding-bottom: 100rpx;"></view>
			</scroll-view>
		</view>
		<view class="" style="height: 400rpx;">
			
		</view>
		<!--输入-->
		<view class="oper flex-row" @tap.prevent.stop="">
			<view class="" style="display: flex;justify-content: space-between;width: 100%;padding: 0 40rpx;margin-top: 30rpx;">
				 <u-icon name="list-dot" color="#000" size="32"></u-icon>
				<input v-if="isEdit" @focus="inputFcus" :focus="isFocus" :cursor-spacing="8" :adjust-position="false" type="text" v-model="content" class="input" placeholder="请输入内容"/>
				<!--发送-->
				<view @touchend.prevent="send"  class="btn">发送</view>
			</view>
			
		</view>
	</view>
</template>

<script>

export default{
	
	data(){
		return {
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
		}
	},
	onLoad(option){
		// 初始化内容高度
		this.setScrollHeight()
		
		// 初始化状态栏高度
		uni.getSystemInfo({
			success: res=>{
				this.statusBarHeight = res.statusBarHeight
			}
		})
	},
	onHide(){
		innerAudioContext.stop()
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
			this.pushMessage(this.content, 'text', ()=>{
				this.content = ''
			})
		},
		// 推送消息
		pushMessage(content, type='text', cb=()=>{}){
			
			// 组合消息体：需要保存在本地数据库的数据
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
					'Authorization':'Bearer' + ' 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'
				},
				method:'POST',
				data: {
					"model": "text-davinci-003",
					"prompt": content,
					  "max_tokens": 1000,
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
.map{
	width: 60%;
	height: 300upx;
	background-color: #FFF !important;
	
	&::before{
		border-right: 30upx solid #FFF !important;
	}
	
	.title{
		height: 80upx;
		font-size: 28upx;
		overflow: hidden;
		text-overflow: ellipsis;
		display: -webkit-box;
		-webkit-line-clamp: 2;
		-webkit-box-orient: vertical;
	}
	
	.box{
		width: 100%;
		height: 170upx;
		margin-top: 10upx;
	}
}
.oper{
	height: 110upx;
	padding: 0 20upx;
	box-sizing: border-box;
	display: flex;
	justify-content: space-around;
	position: fixed;
	bottom: 40rpx;
	width: 100%;
	background: rgba(255, 255, 255, 0.7);
	backdrop-filter: blur(20px);
	
	.input{
		height: 88rpx;
		line-height: 88upx;
		padding: 0 20upx;
		font-size: 28upx;
		border: 2rpx solid #EEE;
		border-radius: 20rpx;
		width: 320rpx;
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
				margin-left: 30upx;
			}
			.content{
				background-color: #A0EA6F;
				
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
