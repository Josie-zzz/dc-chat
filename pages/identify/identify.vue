<template>
	<view class="content">
		<view class="ident-self">
			<image class="image-avater" :src="indexAvater" mode="aspectFill"></image><!-- 微信的头像 -->
			<view class="ident-text">
				<view class="ident-id">会员编号：74369247982342</view>
				<view class="ident-date">首评日期：2020年10月20日</view>
			</view>
			<image :src="indexAvater" mode="aspectFill" class="image-after"></image>
		</view>
		<view class="ident-select">
			<view class="ident-box">
				<view class="iconfont">&#xe60d;</view>
				<view class="ident-msg">我的预约</view>
			</view>
			<view class="ident-box">
				<view class="iconfont">&#xe68a;</view>
				<view class="ident-msg">健康方案</view>
			</view>
			<view class="ident-box">
				<view class="iconfont">&#xe623;</view>
				<view class="ident-msg">报告上传</view>
			</view>
			<view class="ident-box">
				<view class="iconfont">&#xe62d;</view>
				<view class="ident-msg">个人信息</view>
			</view>
		</view>
		<view class="ident-about">
			<view class="kefu">
				<view>联系客服</view>
				<view class="iconfont">&#xe636;</view>
			</view>
			<view class="us">
				<view>关于我们</view>
				<view class="iconfont">&#xe636;</view>
			</view>
			<view class="other">
				<view>其他设置</view>
				<view class="iconfont">&#xe636;</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				indexAvater: getApp().globalData.avatarUrl //当前头像
			}
		},
		methods: {
			
		}
	}
</script>

<style>
	.content {
		display: flex;
		flex-direction: column;
		align-items: center;
		overflow: scroll;
		background-color: #f0f0f0;
	}
	/* 在这个块元素里面实现毛玻璃 */
	.ident-self {
		position: relative;
		display: flex;
		flex-direction: column;
		align-items: center;
		z-index: 1;
		overflow: hidden;
		width: 100%;
		height: 460rpx;
		background-color: #FFFFFF;
	}
	.image-after {
		display: inline;
		content: '';
		position: absolute;
		top: 0;
		left: 0;
		right: 0;
		bottom: 0;
		background-color: rgba(255,255,255,0.5);
		z-index: -1;
		width: 100%;
		height: 100%;
		/* background-image: url('http://gdoctor.xazhima.com/userfiles/upload/2020_07_30/202007301744287095.jpg');
		background-repeat: no-repeat;
		background-position: center;
		background-size: cover; */
		filter: blur(5px);
		/* 解决边缘模糊效果减弱,模糊图片小的问题 */
		/* margin: -100rpx; */
	}
	
	/* 头像 */
	.ident-self .image-avater {
		position: absolute;
		width: 150rpx;
		height: 150rpx;
		display: block;
		border-radius: 50%;
		margin: 30rpx 0;
	}
	.ident-text {
		box-sizing: border-box;
		width: 680rpx;
		height: 250rpx;
		margin-top: 105rpx;
		padding-top: 100rpx;
		background-color: rgba(255,255,255,0.6);
		color: #555555;
		font-size: 16px;
		text-align: center;
		line-height: 26px;
		font-weight: bold;
		border-radius: 15rpx;
		box-shadow: darkgrey 0rpx 0rpx 20rpx 0rpx;
	}
	.ident-select {
		display: flex;
		flex-direction: row;
		flex-wrap: wrap;
		justify-content: space-between;
		box-sizing: border-box;
		width: 680rpx;
		height: 250rpx;
		background-color: #FFFFFF;
		margin-top: 50rpx;
		padding: 60rpx;
		border-radius: 15rpx;
	}
	.ident-box .ident-msg {
		font-size: 24rpx;
		margin-top: 25rpx;
	}
	.ident-box .iconfont {
		font-size: 60rpx;
		text-align: center;
	}
	.ident-about {
		width: 680rpx;
		height: 300rpx;
		background-color: #FFFFFF;
		margin-top: 25rpx;
		border-radius: 15rpx;
	}
	.ident-about>view {
		display: flex;
		flex-direction: row;
		align-items: center;
		justify-content: space-between;
		box-sizing: border-box;
		width: 100%;
		height: 33.33%;
		padding: 0 50rpx;
	}
</style>
