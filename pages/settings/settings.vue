<template>
	
	<view class="u-page">
<view class="tabbar">
			ChatGPT
		</view>
		<view class="" style="padding-top: 100rpx;">
			<u-cell-group>
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
				<u-cell
					@click="openSheet(index)"
					:title="item.title"
					v-for="(item, index) in list"
					:key="index"
					isLink
					v-if="index > 0"
				>
					<image
						slot="icon"
						class="u-cell-icon"
						:src="item.iconUrl"
						mode="widthFix"
					></image>
				</u-cell>
				
			</u-cell-group>
			<u-cell
				@click="openSheet(6)"
				:title="'清除缓存'"
				:key="'5'"
				isLink
			>
				<image
					slot="icon"
					class="u-cell-icon"
					:src="'https://cdn.uviewui.com/uview/demo/actionSheet/open.png'"
					mode="widthFix"
				></image>
			</u-cell>
			
			
			
			<u-action-sheet
				:show="show1"
				@close="show1 = false"
				:actions="actions1"
			>
			</u-action-sheet>
			<u-action-sheet
				:show="show2"
				@close="show2 = false"
				:actions="actions2"
				cancelText="取消"
			>
			</u-action-sheet>
			<u-action-sheet
				:show="show3"
				@close="show3 = false"
				:actions="actions3"
				description="这是一段描述文本,字号偏小,颜色偏淡"
			>
			</u-action-sheet>
			<u-action-sheet
				:show="show4"
				@close="show4 = false"
				title="标题位置"
				:round="10"
			>
				<text style="margin: 10px 20px 30px 20px; color: #303133; font-size: 15px;">这是一段通过slot传入的内容,您可以在此自定义操作面板</text>
			</u-action-sheet>
			<u-action-sheet
				:show="show6"
				@select="selectClick"
				@close="show6 = false"
				:actions="actions6"
				description="清除缓存"
				:round="10"
				safeAreaInsetBottom
				cancelText="取消"
			>
			</u-action-sheet>
			<tui-tabbar :current="current" :unlined="true" :isFixed="true" backdropFilter backgroundColor="rgba(255, 255, 255, 0.7)" :tabBar="tabBar" color="#777" selectedColor="#AC9157" @click="tabbarSwitch"></tui-tabbar>
		</view>
	</view>
</template>
<script>
	import tuiTabbar from "@/components/thorui/tui-tabbar/tui-tabbar.vue"
	export default {
		components:{
				tuiTabbar
			},
		data() {
			return {
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
				actions0: [{
						name: '选项1',
					},
					{
						name: '选项2',
					},
					{
						name: '选项3',
						subname: '描述文本'
					},
				],
				actions1: [{
						name: '选项1',
					},
					{
						loading: true
					},
					{
						name: '选项被禁用',
						disabled: true
					},
				],
				actions2: [{
						name: '选项1',
					},
					{
						name: '选项2',
					},
					{
						name: '选项3',
					},
				],
				actions3: [{
						name: '选项1',
					},
					{
						name: '选项2',
					},
					{
						name: '选项3',
					},
				],
				actions6: [{
						name: '清除部分缓存',
						id: 'normalClear'
					},
					{
						name: '清除全部缓存（包括chat_key）',
						id: 'allClear'
					},
				],
				
				list: [{
						title: '普通使用',
						iconUrl: 'https://cdn.uviewui.com/uview/demo/actionSheet/custom.png'

					},
					{
						title: '设置状态',
						iconUrl: 'https://cdn.uviewui.com/uview/demo/actionSheet/status.png'
					},
					{
						title: '显示取消按钮',
						iconUrl: 'https://cdn.uviewui.com/uview/demo/actionSheet/cancel.png'
					},
					{
						title: '描述内容',
						iconUrl: 'https://cdn.uviewui.com/uview/demo/actionSheet/desc.png'
					},
					{
						title: '显示标题(显示圆角)',
						iconUrl: 'https://cdn.uviewui.com/uview/demo/actionSheet/title.png'
					},
					{
						title: '微信开放能力',
						iconUrl: 'https://cdn.uviewui.com/uview/demo/actionSheet/open.png'
					}
				]
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
			
			if(uni.getStorage("chat_key")) {
				this.key = uni.getStorageSync("chat_key")
			} else {
				this.key = ''
			}
		},
		methods: {
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
				// #ifndef MP
				if (index === 5) return uni.$u.toast('请在微信内预览')
				// #endif
				this[`show${index}`] = true
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
		background-color: #eee;
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