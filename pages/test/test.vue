<template>
	<view>
		<button @click="toSend">发送信息</button>
		<tui-dropdown-list :show="dropdownShow" :top="94" :height="400">
			<template v-slot:selectionbox>
				<view class="" @click="openChoice">
					ssssss
				</view>
			</template>
			<template v-slot:dropdownbox>
				下拉列表内容
			</template>
		</tui-dropdown-list>
	</view>
</template>

<script>
	import tuiDropdownList from "@/components/thorui/tui-dropdown-list/tui-dropdown-list.vue"
	export default {
		data() {
			return {
				dropdownShow: false
			}
		},
		components: {
			tuiDropdownList
		},
		methods: {
			openChoice() {
				this.dropdownShow = !this.dropdownShow
			},
			toSend() {
				uni.request({
					url: 'https://api.openai.com/v1/completions',
					header: {
						'Content-Type': 'application/json',
						'Authorization':'Bearer ' + uni.getStorageSync("chat_key")
					},
					method:'POST',
					data: {
						"model": "text-davinci-003",
						"prompt": "Say this is a test",
						  "max_tokens": 7,
						  "temperature": 0
					},
					success: (res) => {
						console.log(res,'res')
					}
				})
			}
		}
	}
</script>

<style>

</style>
