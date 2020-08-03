<template>
	<view class="login">
		<image src="../../static/img/bg/bg3.png" mode="aspectFill"></image>
		<button class="login-btn" open-type="getUserInfo" @click="login" @getuserinfo="getUserInfo">
			登陆
		</button>
	</view>
</template>

<script>
	var md5 = require("../../common/md5.js");
	export default {
		data () {
			return {
				
			}
		},
		methods: {
			login () {
				// uni.getUserInfo({
				// 	provider: 'weixin',
				// 	success: (infoRes) => {
				// 		console.log('用户昵称为：' + infoRes.userInfo.nickName);
				// 	}
				// });
				uni.login({
				  provider: 'weixin',
				  success: (loginRes) => {
						//成功后，获取code，再将code通过 request 发送给服务器
				    // console.log(loginRes);
						
						uni.request({
							url: getApp().globalData.doctorURL,
							method: 'POST',
							header: {
								'content-type': 'application/x-www-form-urlencoded',
							},
							data: {
								method: 'login',
								code: loginRes.code,
								timestamp: getApp().globalData.globalTimestamp,
								sign: md5('login' + getApp().globalData.globalTimestamp)								
							},
							success: (res) => {
								// 成功后换取 用户唯一标识 OpenID 和 会话密钥 session_key
								console.log(res.data)
								console.log(res.data.msg.msg)
								getApp().globalData.openId = res.data.msg.openId
								getApp().globalData.session_key = res.data.msg.session_key
								//跳转到手机授权页面
								if(res.data.msg.status===1){
									console.log('用户已授权 手机号未绑定')
									//跳到授权页面
									uni.navigateTo({
										url: './auth'
									})
									
								} else if (res.data.msg.status === 2) {
									console.log('用户已授权过了')
									getApp().globalData.authIsTrue = true
									//获取用户应用信息
									this.getUserApp()
									uni.switchTab({
											url: '/pages/index/index'
									});
								}
								
							},
							fail: (res) => {
								console.log('获取授权接口失败')
							}
						})
				  },
					fail: () => {
						console.log('请求code失败')
					}
				});
			},
			getUserInfo(e){
				console.log(e)
				getApp().globalData.avatarUrl = e.detail.userInfo.avatarUrl
				getApp().globalData.nickName = e.detail.userInfo.nickName
				if(getApp().globalData.authIsTrue) {
					getApp().globalData.iv = e.detail.iv
					getApp().globalData.encryptedData = e.detail.encryptedData
				}
			},
			getUserApp(){
				uni.request({
					url: getApp().globalData.doctorURL,
					header: {
						'content-type': 'application/x-www-form-urlencoded'
					},
					method: 'POST',
					data: {
						method: 'get_member_info',
						openId: getApp().globalData.openId,
						timestamp: getApp().globalData.globalTimestamp,
						sign: md5('get_member_info' + getApp().globalData.globalTimestamp)		
					},
					success: (res) => {
						console.log(res)
						getApp().globalData.uid = res.data.msg.id
						getApp().globalData.roomId = res.data.msg.rooms[0]
					},
					fail: () => {
						console.log('获取应用中用户信息失败')
					}
				})
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
		background-color: #FFFFFF;
		text-align: center;
		line-height: 60rpx;
		color: #ce5e5a;
		letter-spacing: 10rpx;
		font-size: 32rpx;

	}
		
</style>
