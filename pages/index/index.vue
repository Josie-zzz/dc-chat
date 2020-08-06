<template>
	<view class="content">
		<image src="../../static/img/bg/bg5.png" mode="aspectFill"></image>
		<view class="low-content">
			<view class="top box" @click="topTo">
				<view class="iconfont">&#xe601;</view>
				<view class="top-text">在线咨询</view>
			</view>
			<view class="middin box" @click="middinTo">
				<view class="iconfont">&#xe62d;</view>
				<view class="middin-text">预约健康咨询服务</view>
			</view>
			<view class="bottom box" @click="bottomTo">
				<view class="iconfont">&#xe662;</view>
				<view class="middin-text">查阅健康方案</view>
			</view>
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

	.content image {
		position: fixed;
		width: 100%;
		height: 100%;
	}
	.low-content {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: space-around;
		
		padding-top: 380rpx;
	}
 .box {
		display: flex;
		flex-direction: row;
		align-items: center;
		justify-content: space-between;
		position: relative;
		box-sizing: border-box;
		z-index: 1;
		overflow: hidden;
		width: 650rpx;
		height: 180rpx;
		padding: 50rpx;
		margin-bottom: 50rpx;
		border-radius: 15rpx;
		background-color: rgba(255,255,255,0.8);
		font-size: 18px;
		color: #555555;
		box-shadow: darkgrey 0rpx 0rpx 20rpx 0rpx;
 }
 .box .iconfont {
	 font-size: 75rpx;
 }

</style>
