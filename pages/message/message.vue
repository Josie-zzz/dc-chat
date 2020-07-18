<template>
	<view class="content">
		<view class="msg-bg"><!-- 显示信息的容器 -->
			<view class="msg">
				<view class="msg-item">	<!-- 每条信息的个体 -->
					<image src="../../static/img/bg/bg1.jpg" mode=""></image>
					<view class="msg-text">
						<text>您已经成功预约，现在的时间为2020.7.14</text>
						<view class="msg-status"></view>
					</view>
				</view>
				<view class="msg-item">	<!-- 每条信息的个体 -->
					<image src="../../static/img/bg/bg1.jpg" mode=""></image>
					<view class="msg-text">
						<text>您已经成功预约，现在的时间为2020.7.14</text>
						<view class="msg-status"></view>
					</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				
			}
		},
		methods: {
			
		}
	}
</script>

<style>
	.content {
		display: flex;
		flex-direction: row;
		justify-content: center;
		align-items: center;
		padding: 20rpx;
		box-sizing: border-box;
	}
	.msg-bg {
		width: 100%;
		height: 100%;
		border-radius: 15rpx;
		background-color: #FFFFFF;
		padding:40rpx 0;
		box-sizing: border-box;
	}
	.msg {
		padding: 0 40rpx;
		width: 100%;
		height: 100%;
		box-sizing: border-box;
		overflow: scroll;
	}
	.msg-item {
		display: flex;
		flex-direction: row;
		margin-top: 30rpx;
	}
	.msg-item image {
		width: 88rpx;
		height: 88rpx;
		border-radius: 44rpx;
	}
	.msg-text {
		height: 70rpx;
		font-size: 28rpx;
		flex: 1;
		margin-left: 20rpx;
		padding: 10rpx;
		border-bottom: #afafaf dashed 3rpx;
		position: relative;
	}
	.msg-status {
		width: 14rpx;
		height: 14rpx;
		border-radius: 7rpx;
		background-color: #ff0000;
		position: absolute;
		top: 0rpx;
		right: 0rpx;
	}
</style>
