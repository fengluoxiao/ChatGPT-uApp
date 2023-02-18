<template>
	<view class="footer">
		<view class="footer-left">
			<!-- <view class="uni-icon uni-icon-mic" @tap="startRecognize"> </view> -->
			<u-icon name="list-dot" size="32" color="#000" @click="showDownList"></u-icon>
		</view>
		<view class="footer-center">
			<input class="input-text" type="text" v-model="inputValue"></input>
		</view>
		<view class="footer-right" >
			<tui-button type="green" height="80rpx" @tap="sendMessge">发送</tui-button>
		</view>
	</view>
</template>

<script>
	import tuiButton from "@/components/thorui/tui-button/tui-button.vue"
	export default {
		name: "chat-input",
		data() {
			return {
				inputValue: '',
				
			}
		},
		components: {
			tuiButton
		},
		methods: {
			startRecognize: function () {
				var options = {};
				var that = this;
				options.engine = 'iFly';
				that.inputValue = "";
				plus.speech.startRecognize(options, function (s) {
					console.log(s);
					that.inputValue += s;
				}, function (e) {
					console.log("语音识别失败：" + e.message);
				});
			},
			sendMessge: function () {
				var that = this;
				if (that.inputValue.trim() == '') {

					that.inputValue = '';
				} else {

					//点击发送按钮时，通知父组件用户输入的内容
					this.$emit('send-message', {
						type: 'text',
						content: that.inputValue
					});
					uni.pageScrollTo({
						scrollTop: 99999
					})
					that.inputValue = '';
				}
			}
		}
	}
</script>

<style scoped>
	@import "../common/icon.css";

	.footer {
		display: flex;
		flex-direction: row;
		width: 100%;
		height: 60px;
		min-height: 60px;
		overflow: hidden;
		
		padding: 5px 5px 20px 5px;
		
	}
	.footer-left {
		width: 80px;
		height: 60px;
		display: flex;
		justify-content: center;
		align-items: center;
	}
	.footer-right {
		width: 120rpx;
		height: 60px;
		display: flex;
		justify-content: center;
		align-items: center;
		color: #1482D1;
		margin: 0 45rpx;
	}
	.footer-center {
		flex: 1;
		height: 60px;
		display: flex;
		justify-content: center;
		align-items: center;
	}
	.footer-center .input-text {
		flex: 1;
		background: #fff;
		border: solid 1px #ddd;
		padding: 10px !important;
		font-family: verdana !important;
		overflow: hidden;
		border-radius: 15px;
	}
</style>
