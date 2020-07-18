<template>
	<view class="content">
		<view class="top box" @click="topTo">
			在线咨询
		</view>
		<view class="middin box" @click="middinTo">
			预约健康咨询服务
		</view>
		<view class="bottom box" @click="bottomTo">
			查阅健康方案
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				title: 'Hello'
			}
		},
		onLoad() {

		},
		methods: {
			topTo () {
				uni.navigateTo({
					url: '../consult/online'
				})
			},
			middinTo () {
				uni.navigateTo({
					url: '../consult/appoint'
				})
			},
			bottomTo () {
				uni.navigateTo({
					url: '../consult/find'
				})
			} 
		}
	}
</script>

<style>
	.content {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: space-around;
	}

 .box {
	 color: #FFFFFF;
	 font-size: 50rpx;
	 font-weight: bold;
	 line-height: 280rpx;
	 text-align: center;
	 width: 680rpx;
	 height: 280rpx;
	 border-radius: 20rpx;
 }
 
 .top, .bottom {
	 letter-spacing: 10rpx;
 }
 
 .top {
	 background-color: #065085;
 }
 
 .middin {
	 background-color: #990033;
 }
 
 .bottom {
	 background-color: #ff9900;
 }
</style>
