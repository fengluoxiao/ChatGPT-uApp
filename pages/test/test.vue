<template>
	<view>
		<button @click="toSend">发送信息</button>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				
			}
		},
		methods: {
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
