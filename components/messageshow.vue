<template>
	<view class="m-item" :id="'message'+id">
		<view class="m-left">
			<image class="head_icon" src="../static/img/e3d955c27d7943b9832e0f35d50f5193.png" v-if="message.user=='home'"></image>
		</view>
		<view class="m-content">
			<view class="m-content-head" :class="{'m-content-head-right':message.user=='customer'}">
				<view :class="'m-content-head-'+message.user">{{message.content}} </view>
			</view>
		</view>
		<view class="m-right">
			<image class="head_icon" :src="abosultPaht+user.customer.img" v-if="message.user=='customer'"></image>
		</view>
	</view>
</template>

<script>
	import {
		mapState
	} from 'vuex'

	export default {
		data() {
			return {
				abosultPaht: "../../"
			}
		},
		props: ['message', 'id'],
		computed: mapState(['user'])
	}
</script>

<style>
	.m-item {
		display: flex;
		flex-direction: row;
		padding-top: 40px;
	}
	.m-left {
		display: flex;
		width: 120rpx;
		justify-content: center;
		align-items: flex-start;
	}
	.m-content {
		display: flex;
		flex: 1;
		flex-direction: column;
		justify-content: center;
		height: 100%;
	}
	.m-right {
		display: flex;
		width: 120rpx;
		justify-content: center;
		align-items: flex-start;
	}
	.head_icon {
		width: 80rpx;
		height: 80rpx;
	}
	.m-content-head {
		position: relative;
	}
	.m-content-head-right {
		display: flex;
		justify-content: flex-end;
	}
	.m-content-head-home {
		text-align: left;
		background: #1482d1;
		border: 1px #1482d1 solid;
		border-radius: 10px;
		padding: 10px;
		color: white;
		display: inline-block !important;
	}

	.m-content-head-home:before {
		border: 10px solid transparent;
		border-right: 10px solid #1482d1;
		left: -18px;
		width: 0;
		height: 0;
		position: absolute;
		content: ' '
	}
	.m-content-head-customer {
		border: 1px white solid;
		background: #eee;
		border-radius: 10px;
		padding: 10px;
	}
	.m-content-head-customer:after {
		border: 10px solid transparent;
		border-left: 10px solid #eee;
		top: 12px;
		right: -18px;
		width: 0;
		height: 0;
		position: absolute;
		content: ' '
	}
</style>
