<template>
	<view class="content">
		<view class="box">
			<scroll-view class="msg-scroll" scroll-y="true" :scroll-into-view="scrollToId" scroll-top="100">
				<block v-for="(userMsg, index) in room_list " :key="userMsg.id" >
					
					<!-- 此处本来应该判断五分钟内显示事件，但是时间从后台返回过来是被处理过的， -->
					<view class="msg-time">
						{{ userMsg.date }}
					</view>
					<!-- 自己发的消息 -->
					<view v-if=" userMsg.uid === '1' " class="my-msg" :id=" 'msg'+ userMsg.id ">
						<!-- 头像 -->
						<view class="avater right">
							<image src="../../static/img/avater/audrey.png" mode="aspectFill"></image>
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
					<view v-else class="you-msg" :id=" 'msg'+ userMsg.id ">
						<!-- 头像 -->
						<view class="avater left">
							<image src="../../static/img/avater/andy.png" mode="aspectFill"></image>
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
			</scroll-view>
		</view>
		<!-- 模仿微信的一个底部发送框 -->
		<view class="send-msg-bar">	
			<!-- 发语音 -->
			<text class="send-voice iconfont send-icon bar-left">&#xe68d;</text>
			<!-- 转换键盘 -->
			<text class="send-keyBoard iconfont send-icon bar-left" style="display: none;">&#xe65b;</text>
			<textarea class="send-edit" v-model="msg"></textarea>
			<view class="bar-right">
				<!-- 发表情 -->
				<text class="send-emoji iconfont send-icon">&#xe609;</text>
				<!-- 添加多选 -->
				<text class="send-add iconfont send-icon" v-show="msg === ''">&#xe631;</text>
				<!-- 发送按钮 -->
				<button class="send-btn" v-show="msg !== ''" @click="sendMsg">发送</button>
			</view>
		</view>

	</view>
</template>

<script>
	var md5 = require("../../common/md5.js"); //加密信息
	import parseHtml from '../../common/html-parser.js' //将 HTML String转化为 nodes 数组
	
	var time = 0
	
	export default {
		components: {
		},
		data() {
			return {
				room_list: null ,							//储存聊天消息列表
				msg: '',	//储存当前输入的信息 
				scrollToId: '',
			}
		},
		onLoad() {
			//建立socket连接
			this.createSocket()
			
			// 获取历史聊天记录
			this.getChatList()	
		},
		onShow() {
			
		},
		methods: {
			getChatList () {	//获取聊天消息
				uni.request({
				    url: 'http://gdoctor.xazhima.com/api/v1.0/api.php', 
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
								console.log('获取聊天列表成功');
								console.log(res.data.msg)
								
								this.room_list = res.data.msg
								this.$nextTick(() => {
									//进入页面滚动到底部
									this.scrollToId = 'msg' + this.room_list[this.room_list.length - 1].id
									console.log('zhe....' + this.scrollToId)
								});
							}	
				    },
						fail: (res) => {
							console.log('请求失败哦')
						}
				});
			},
			createSocket () {
				let sendData = {
					type: "login",
					uid: "1",
					client_name: "1",
					room_id: "2"
					}
				uni.connectSocket({
					url: 'wss://gdoctor.xazhima.com/wss',				//注意：接口成功不意味着链接就打开了
					success: () => {					//接口调用成功
						uni.onSocketOpen( (res) => {					//监听链接是否打开
						  console.log('WebSocket连接已打开！');
							uni.sendSocketMessage({
								data: JSON.stringify(sendData),
								success: () => {
									console.log('成功了。。。。。')
									uni.onSocketMessage( (res) => {
									  console.log('收到服务器内容：' + res.data);	//String类型
										let msg = JSON.parse(res.data)
										if(msg.uid && msg.content){
											//向显示列表中push
											this.room_list.push(msg)
											this.$nextTick(() => {
												// 滚动到底
												this.scrollToId = 'msg' + msg.id;
											});
										}
									});
								},
								fail: () => {
									console.log('失败了。。。。。')
								}
							})
						});
						uni.onSocketError(function (res) {
						  console.log('WebSocket连接打开失败，请检查！');
						});
					},
					fail: () => {
						console.log('接口请求失败！')
					}
				})
			},
			sendMsg(){
				const msg = this.msg
				//发送请求
				uni.request({
					url: 'http://gdoctor.xazhima.com/api/v1.0/api.php',
					method:'POST',
					header: {
						'content-type': 'application/x-www-form-urlencoded'
					},
					data: {
						method: 'room_msg_add',
						timestamp: getApp().globalData.globalTimestamp, 
						uid:'1',
						sign: md5('room_msg_add' + getApp().globalData.globalTimestamp),
						content: msg,
						extra: Date.now(),
						size:'',
						msg_type:'1',
						room_id: '2',
					},
					success: (res) => {
						console.log('发送消息成功')
						console.log(res)
						const addMsg = {
							id: res.data.msg,
							uid: '2',
							uname: '小李',
							content: msg
						}
						
						//清空输入框
						this.msg = ''
					},
					fail: () => {
						console.log('发送消息失败')
					}
				})
			}
			
		},
	}
</script>

<style>
	.content {
		display: flex;
		flex-direction: column;
	}
	.box {
		width: 100%;
		flex: 1;
		overflow: scroll;
	}
	.msg-scroll {
		height:100%;
		display: flex;
		flex-direction: column;
	}
	.my-msg, .you-msg {
		padding: 10px 0;
	}
	/* 解决float脱离文档流高度塌陷问题 */
	.my-msg:after, .you-msg::after { 
	  content:""; 
	  display: block; 
	  clear:both; 
	}
	/* 右边消息浮动 */
	.right {
		float: right;
		margin-right: 10px;
	}
	/* 左边消息浮动 */
	.left {
		float: left;
		margin-left: 10px;
	}
	/* 头像 */
	.avater {
		width: 36px;
		height: 36px;
		border-radius: 4px;
		background-color: #FFFFFF;
	}
	/* 头像图皮 */
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
	
	.send-msg-bar {
		z-index: 100;
		width: 100%;
		display: flex;
		flex-direction: row;
		align-items: center;
		justify-content: space-around;
		height: 50px;
		background-color: #f8f8f8;
	}
	.send-edit {
		font-size: 15px;
		width: 600rpx;
		height: 36px;
		padding: 5px;
		min-height: 36px;
		line-height: 26px;
		background-color: #ffffff;
		border-radius: 6px;
		box-sizing: border-box;
	}
	.send-icon {
		font-size: 26px;
	}
	.send-btn {
		font-size: 16px;
		width: 60px;
		height: 30px;
		line-height: 30px;
		background-color: #05c060;
		color: #ffffff;
	}
	.bar-left, .bar-right{
		margin: 0 10px;
	}
	.bar-right {
		display: flex;
		flex-direction: row;
		align-items: center;
	}
	.bar-right .send-emoji {
		margin-right: 10px;
	}
	.msg-time {
		text-align: center;
		font-size: 10px;
		color: #9c9c9c;
		line-height: 30px;
	}
</style>
