<template>
	<view class="login">
		<image src="../../static/img/bg/bg3.png" mode="aspectFill"></image>
		<view class="login-btn" @click="login">
			登陆
		</view>
	</view>
</template>

<script>
	export default {
		data () {
			return {
				
			}
		},
		methods: {
			login () {
				uni.getUserInfo({
					provider: 'weixin',
					success: (infoRes) => {
						console.log('用户昵称为：' + infoRes.userInfo.nickName);
					}
				});
				// uni.login({
				//   provider: 'weixin',
				//   success: (loginRes) => {
				// 		console.log('success')
				//     console.log(loginRes);
				//   },
				// 	fail: () => {
				// 		console.log('fail')
				// 	}
				// });
				
				// console.log('ok!!!!')
				// uni.switchTab({
				// 	url: 'pages/index/index',
				// 	success: () => {
				// 		console.log('success')
				// 	},
				// 	fail: () => {
				// 		console.log('fail')
				// 	}
				// })
			}
		}
	}
</script>

<style>

	.login, .login image {
		width: 100%;
		height: 100%;
	}
	.login {
		position: relative;
	}
	.login-btn {
		position: absolute;
		top: 50%;
		left: 38%;
		width: 180rpx;
		height: 60rpx;
		border: #ce5e5a solid 1px;
		border-radius: 30rpx;
		text-align: center;
		line-height: 60rpx;
		color: #ce5e5a;
		letter-spacing: 10rpx;
		font-size: 32rpx;

	}
		
</style>
