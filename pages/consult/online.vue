<template>
	<view class="content">
		
		<block v-for="userMsg in room_list " :key="userMsg.id">
			<!-- 自己发的消息 -->
			<view v-if=" userMsg.uid === '1' " class="my-msg">
				<!-- 头像 -->
				<view class="avater right">
					<!-- <image src="" mode=""></image> -->
				</view>
				<!-- 文本消息 -->
				<view class="right">
					<!-- 显示用户名 -->
					<view style="padding-right: 4px;margin-bottom: 4px;font-size: 10px;color: #9c9c9c;text-align: right;">{{ userMsg.uname }}</view>
					<!-- 消息气泡 -->
					<view class="msg-content bubble bubble-r"> 
						<!-- 富文本显示 -->
						<!-- <rich-text :nodes="nodes"></rich-text> -->
						<text class="bubble-txt">{{ userMsg.content }}</text>
					</view>	
				</view>
			</view>
			
			<!-- 对方发的消息 -->
			<view v-else class="you-msg">
				<!-- 头像 -->
				<view class="avater left">
					<image src="" mode=""></image>
				</view>
				<!-- 文本消息 -->
				<view class="left">
					<!-- 显示用户名 -->
					<view style="padding-left: 4px;margin-bottom: 4px;font-size: 10px;color: #9c9c9c;text-align: left;">{{ userMsg.uname }}</view>
					<!-- 消息气泡 -->
					<view class="msg-content bubble bubble-l"> 
						<!-- 富文本显示 -->
						<!-- <rich-text :nodes="nodes"></rich-text> -->
						<text class="bubble-txt">{{ userMsg.content }}</text>
					</view>
				</view>
			</view>
		</block>
		
		<!-- 固定在底部的发送消息窗口 -->
		<SendMsgBar></SendMsgBar>
	</view>
</template>

<script>
	var md5 = require("../../common/md5.js"); //加密信息
	import parseHtml from '../../common/html-parser.js' //将 HTML String转化为 nodes 数组
	import SendMsgBar from '../../components/sendMsgBar.vue'
	
	export default {
		components: {
			SendMsgBar
		},
		data() {
			return {
				room_list: null ,							//储存聊天消息列表
			}
		},
		onLoad() {
			//建立socket连接
			this.createSocket()
			
			// 获取历史聊天记录
			this.getChatList()	
		},
		methods: {
			getChatList () {	//获取聊天消息
				uni.request({
				    url: 'http://gdoctor.xazhima.com/api/v1.0/api.php', //仅为示例，并非真实接口地址。
						method: 'POST',
						header: {
							'content-type': 'application/x-www-form-urlencoded'
						},
				    data: {
							method: 'room_msg_list',
				      room_id: '2', 						//房间号
							uid: '1',									//自己的聊天id
							current_msg_id: '',
							direction: '',
							page: '1',
							pageSize: '15',
							timestamp: getApp().globalData.globalTimestamp,
							sign: md5('room_msg_list' + getApp().globalData.globalTimestamp)
				    },
				    
				    success: (res) => {
							if(res.data.code === 200){
								this.room_list = res.data.msg
								console.log('获取聊天列表成功');
								console.log(res.data);
							}	
				    },
						fail: (res) => {
							console.log('请求失败哦')
						}
				});
			},
			createSocket () {
				uni.connectSocket({
					url: 'wss://gdoctor.xazhima.com/wss',
					success: () => {
						console.log('success')
					},
					fail: () => {
						console.log('error')
					}
				})
				uni.onSocketOpen(function (res) {
				  console.log('WebSocket连接已打开！');
				});
				uni.onSocketError(function (res) {
				  console.log('WebSocket连接打开失败，请检查！');
				});
			}
		},
		
	}
</script>

<style>
	.content {
		position: relative;
	}
	/* .send-content {
		overflow: scroll;
	} */
	.content {
		/* 不仅可以滑动聊天内容,还可以解决margin高度塌陷问题 */
		overflow: scroll;
	}
	.my-msg, .you-msg {
		margin: 10px 0;
	}
	/* 解决脱离文档流高度塌陷问题 */
	.my-msg:after, .you-msg::after { 
	  content:""; 
	  display: block; 
	  clear:both; 
	}
	.right {
		float: right;
		margin-right: 10px;
	}
	.left {
		float: left;
		margin-left: 10px;
	}
	.avater {
		width: 36px;
		height: 36px;
		border-radius: 4px;
		background-color: #FFFFFF;
	}
	.avater image {
		width: 100%;
		height: 100%;
	}
	/* 气泡中文字 */
	.bubble-txt {
		font-size: 16px;
		/* 处理单词换行 */
		word-break: break-all; 
		color: #000000;
		line-height: 26px;
	}
	.bubble {
		position: relative;
		max-width: 450rpx; /* 机型不同,长度也应该有所变化 */
		padding: 5px 10px;
		background-color: #96ed6a;
		border-radius: 4px;
	}
	.bubble-r:after{
		position: absolute;
		top: 13px;
		left: 100%;
		content: '';
		width: 0;
		height: 0;
		border-top: 5px solid transparent;
		border-bottom: 5px solid transparent;
		border-left: 5px solid #96ed6a;
	}
	.bubble-l::before{
		position: absolute;
		top: 13px;
		right: 100%;
		content: '';
		width: 0;
		height: 0;
		border-top: 5px solid transparent;
		border-bottom: 5px solid transparent;
		border-right: 5px solid #96ed6a;
	}
</style>
