<template>
	<view class="content">
		<view class="ident-self">
			<image src="../../static/img/bg/bg1.jpg" mode=""></image><!-- 微信的头像 -->
			<view class="ident-text">
				<view class="ident-id">会员编号：74369247982342</view>
				<view class="ident-date">首评日期：2020年10月20日</view>
			</view>
		</view>
		<view class="ident-select">
			<view class="ident-box">
				<text class="iconfont">&#xe60d;</text>
				<view class="ident-msg">我的预约</view>
			</view>
			<view class="ident-box">
				<text class="iconfont">&#xe68a;</text>
				<view class="ident-msg">健康方案</view>
			</view>
			<view class="ident-box">
				<text class="iconfont">&#xe623;</text>
				<view class="ident-msg">报告上传</view>
			</view>
			<view class="ident-box">
				<text class="iconfont">&#xe62d;</text>
				<view class="ident-msg">个人信息</view>
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
		flex-direction: column;
		align-items: center;
		overflow: scroll;
	}
	.ident-select, .ident-self {
		width: 660rpx;
		border-radius: 20rpx;
	}
	.ident-self {
		display: flex;
		flex-direction: column;
		align-items: center;
		color: #c26e41;
		font-size: 32rpx;
		line-height: 50rpx;
		font-weight: bold;
		height: 380rpx;
		background-color: #f6f6f6;
		margin-top: 60rpx;
		box-shadow: 2rpx 2rpx 5rpx 2rpx #cdcdcd;
	}
	.ident-self image {
		width: 150rpx;
		height: 150rpx;
		display: block;
		border-radius: 75rpx;
		margin: 30rpx 0;
	}
	.ident-select {
		display: flex;
		flex-direction: row;
		flex-wrap: wrap;
		justify-content: space-between;
		/* height: 380rpx; */
		width: 540rpx;
		background-color: #FFFFFF;
		margin-top: 70rpx;
		box-shadow: 0rpx 0rpx 5rpx 2rpx #cdcdcd;
		padding: 60rpx;
		padding-top: 0;
	}
	.ident-box {
		margin-top: 60rpx;
		width: 33.33%;
		text-align: center;
	}
	.ident-box .ident-msg {
		font-size: 32rpx;
		line-height: 60rpx;
	}
	.ident-box .iconfont {
		font-size: 70rpx;
		display: block;
	}
</style>
