<template>
	<!-- 授权页面 -->
	<view class="content">
		<view class="top">
			<image src="../../static/img/bg/bg1.jpg" mode="aspectFill"></image>
			<view class="auth-text">广庭上医</view>
		</view>
		<view class="bottom">
			<view class="auth-text">请确认以授权信息</view>
			<label class="radio" @click="() => {this.checked = !this.checked}"><checkbox :checked="checked" />获取你的信息（昵称、头像等）</label>
			<button type='primary' :disabled="!checked" open-type="getPhoneNumber" @getphonenumber="getphonenumber">授权</button>
		</view>
		<uni-popup ref="popup" type="dialog">
		    <uni-popup-dialog type="input" mode="base" :content="authStatusInfo" :duration="2000" :before-close="true" @close="close" @confirm="confirm"></uni-popup-dialog>
		</uni-popup>
	</view>
</template>

<script>
	import uniPopup from '@/components/uni-popup/uni-popup.vue'
	import uniPopupMessage from '@/components/uni-popup/uni-popup-message.vue'
	import uniPopupDialog from '@/components/uni-popup/uni-popup-dialog.vue'
	var md5 = require("../../common/md5.js");
	export default {
		data () {
			return {
				checked: false,
				authStatusInfo: '',
				authStatus: false //true表示成功授权，可以进入小程序了
			}
		},
		components:{
			uniPopup,
			uniPopupMessage,
			uniPopupDialog
		},
		onLoad() {
		},
		methods: {
			close(done){
				done()
			},
			confirm(done,value){
				//授权成功，跳到主页
				if(this.authStatus){
					uni.switchTab({
							url: '/pages/index/index'
					});
					this.getUserApp()
					console.log('授权成功，并跳到了主页！')
				} else {
					uni.navigateBack({
						delta: 1
					})
					getCurrentPages()
					console.log('授权失败，并跳回到登陆页面')
				}
				done()
			},
			getphonenumber (e) {
				//手机授权，并且获得iv和encryptedData（加密数据参数）
				console.log('返回iv和encryptedData')
				console.log(e)
				if(e.detail.errMsg === 'getPhoneNumber:ok'){
					getApp().globalData.iv = e.detail.iv
					getApp().globalData.encryptedData = e.detail.encryptedData
					//用户授权请求
					this.getAuth()
				}
			},
			getAuth() {
				uni.request({
					url: getApp().globalData.doctorURL,
					method: 'POST',
					header: {
						'content-type': 'application/x-www-form-urlencoded'
					},
					data: {
						method: 'auth',
						iv: getApp().globalData.iv,
						encryptedData: getApp().globalData.encryptedData,
						session_key: getApp().globalData.session_key,
						openId: getApp().globalData.openId,
						timestamp: getApp().globalData.globalTimestamp, 
						sign: md5('auth' + getApp().globalData.globalTimestamp)
					},
					success: (res) => {
						console.log('请求授权后返回的信息：')
						console.log(res)
						//存当前登陆状态信息
						this.authStatusInfo = `<span>${res.data.msg}</span>`
						console.log(this.authStatusInfo)
						if(res.data.code === 200){
							console.log('手机号绑定成功')
							this.authStatus = true
							this.$refs.popup.open()
							
						} else if(res.data.code === 101){
							console.log('授权失败')
							this.authStatus = false
							this.$refs.popup.open()
						}
					},
					fail: (res) => {
						console.log('fail')
					}
				})
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
					},
					fail: () => {
						console.log('fail')
					}
				})
			}
		}
	}
</script>

<style>
	.content {
		background-color: #FFFFFF;
	}
	.content, .top, .bottom {
		display: flex;
		flex-direction: column;
		align-items: center;
	}
	.top, .bottom {
		width: 600rpx;
	}
	.top {
		margin-top: 70rpx;
	}
	.auth-text {
		font-size: 16px;
		line-height: 35px;
	}
	.top image {
		width: 70px;
		height: 70px;
		border-radius: 50%;
	}
	.bottom {
		padding-top: 40rpx;
		margin-top: 30rpx;
		border-top: solid #f2f2f2 2px;
	}
	.bottom button {
		width: 100%;
	}
	.radio {
		line-height: 80rpx;
		font-size: 12px;
		color: #7d7d7d;
	}
	.radio checkbox {
		transform: scale(0.7)
	}
</style>
