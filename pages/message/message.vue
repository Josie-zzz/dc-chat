<template>
	<view class="content">
		<view class="msg-bg"><!-- 显示信息的容器 -->
			<view class="msg">
				
				<view class="msg-item">	<!-- 每条信息的个体 -->
					<image src="../../static/img/bg/bg1.jpg" mode=""></image>
					<view class="msg-text">
						<view class="msg-left">
							<view style="font-size: 16px;line-height: 25px;">系统消息</view>
							<view style="font-size: 12px;line-height: 20px;color: #b1b1b1;">您已经成功预约，现在的时间为2020.7.14</view>
						</view>
						<view class="msg-right">
							15:57
						</view>
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
		box-sizing: border-box;
		background-color: #FFFffF;
	}
	.msg-bg {
		width: 100%;
		height: 100%;
		box-sizing: border-box;
	}
	.msg {
		width: 100%;
		height: 100%;
		box-sizing: border-box;
		overflow: scroll;
	}
	.msg-item {
		display: flex;
		flex-direction: row;
		align-items: center;
		width: 100%;
		height: 70px;
	}
	.msg-item image {
		width: 45px;
		height: 45px;
		margin: 0 15px;
		border-radius: 5px;
	}
	.msg-text {
		display: flex;
		flex-direction: row;
		justify-content: space-between;
		flex: 1;
		box-sizing: border-box;
		height: 100%;
		padding: 10px 0;
		border-bottom: #eaeaea solid 1px;
	}
	.msg-left {
		flex: 1
	}
	.msg-right {
		width: 50px;
		font-size: 10px;
		line-height: 20px;
		color: #b1b1b1;
	}
</style>
