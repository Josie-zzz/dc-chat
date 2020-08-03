<template>
	<view class="content">
		<view class="box" @click="() => {showNum = 0}">
			<scroll-view class="msg-scroll" scroll-y="true" :scroll-into-view="scrollToId" scroll-top="100">
				<block v-for="(userMsg, index) in room_list " :key="userMsg.id" >
					
					<!-- 此处本来应该判断五分钟内显示事件，但是时间从后台返回过来是被处理过的， -->
					<view class="msg-time">
						{{ userMsg.date }}
					</view>
					<!-- 自己发的消息 -->
					<view v-if=" userMsg.uid === uid " class="my-msg" :id=" 'msg'+ userMsg.id ">
						<!-- 头像 -->
						<view class="avater right">
							<image :src="avatarUrl" mode="aspectFill"></image>
						</view>
						<!-- 文本消息 -->
						<view class="right">
							<!-- 显示用户名 -->
							<view style="padding-right: 4px;margin-bottom: 4px;font-size: 10px;color: #9c9c9c;text-align: right;">{{ userMsg.uname }}</view>
							<!-- 消息气泡 -->
							<view class="msg-content bubble bubble-r"> 
								<!-- 富文本显示 -->
								<view class="bubble-txt" v-if="userMsg.msg_type === '1'">
									<rich-text :nodes="userMsg.content.replace(reg, emotion)"></rich-text>
								</view>
								<!-- 图片消息 -->
								<view class="bubble-pic" v-if="userMsg.msg_type === '4'">
									<image v-if="userMsg.content" :src="userMsg.content" mode="widthFix" style="width: 300rpx;border-radius: 10rpx;" @click="previewImage"></image>
								</view>
								<!-- 文件消息  -->
								<view v-if=" userMsg.msg_type === '5' " class="bubble-file iconfont" style="font-size: 50rpx;color: #0072ca;" @click="openFile(userMsg.content)">
									&#xe662;
								</view>
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
								<view class="bubble-txt" v-if="userMsg.msg_type === '1'">
									<rich-text :nodes="userMsg.content.replace(reg, emotion)"></rich-text>
								</view>
								<!-- 图片消息 -->
								<view class="bubble-pic" v-if="userMsg.msg_type === '4'">
									<image v-if="userMsg.content" :src="userMsg.content" mode="widthFix" style="width: 300rpx;border-radius: 10rpx;" @click="previewImage(userMsg.content)"></image>
								</view>
								<!-- 文件消息  -->
								<view v-if=" userMsg.msg_type === '5' " class="bubble-file iconfont" style="font-size: 50rpx;color: #0072ca;" @click="openFile(userMsg.content)">
									&#xe662;
								</view>
							</view>
						</view>
					</view>
				</block>
			</scroll-view>
		</view>
		<!-- 模仿微信的一个底部发送框 -->
		
		<!-- 底部输入框 -->
		<view class="footer">
			<!-- 输入框 -->
			<view class="send-msg-bar">
				<!-- 发语音 -->
				<text class="send-voice iconfont send-icon bar-left">&#xe68d;</text>
				<!-- 转换键盘 -->
				<text class="send-keyBoard iconfont send-icon bar-left" style="display: none;">&#xe65b;</text>
				<!-- 输入框信息 -->
				<textarea class="send-edit" v-model="msg" :auto-height="true" :show-confirm-bar="false" :maxlength="-1" cursor-spacing="10"></textarea>
				<view class="bar-right">
					<!-- 发表情 -->
					<text class="send-emoji iconfont send-icon" @click="() => {showNum = 1}">&#xe609;</text>
					<!-- 添加多选 -->
					<text class="send-add iconfont send-icon" v-show="msg === ''" @click="() => {showNum = 2}">&#xe631;</text>
					<!-- 发送按钮 -->
					<button class="send-btn" v-show="msg !== ''" @click="sendMsg(1, '')">发送</button>
				</view>
			</view>
			<!-- 多选框 -->
			<view class="msg-other">
				<!-- 选择表情 -->
				<Emotion :showNum="showNum" @emotion="handleEmotion" :height="200"></Emotion>
				<!-- 发送其他媒体 -->
				<view v-show="showNum === 2" class="msg-plus">
					<!-- 上传图片 -->
					<view class="iconfont other" @click="chooseImage">
						&#xe8ba;
					</view>
					<!-- 上传文件 -->
					<view class="iconfont other" @click="chooseFile">
						&#xe662;
					</view>
				</view>
			</view>
		</view>
		

	</view>
</template>

<script>
	var md5 = require("../../common/md5.js"); //加密信息
	//import parseHtml from '../../common/html-parser.js' //将 HTML String转化为 nodes 数组
	import Emotion from '../../components/Emotion/index.vue'
	
	export default {
		components: {
			Emotion
		},
		data() {
			return {
				room_list: null ,							//储存聊天消息列表
				msg: '',	//储存当前输入的信息 
				scrollToId: '',
				reg: /\#[\S]{1,3}\;/gi,
				showNum: 0, //显示当前的模块，0是不显示，1显示表情，2显示多媒体多选
				avatarUrl: getApp().globalData.avatarUrl, //当前用户头像
				uid: getApp().globalData.uid,  //当前用户id
			}
		},
		computed:{
			
		},
		onLoad() {
			//建立socket连接
			this.createSocket()
			
			// 获取历史聊天记录
			this.getChatList()	
		},
		onUnload() {
			// 关闭socket链接
			uni.closeSocket({
			  url: 'wss://gdoctor.xazhima.com/wss'
			});
			uni.onSocketClose(function (res) {
			  console.log('WebSocket 已关闭！');
			});
		},
		methods: {
			getChatList () {	//获取聊天消息
				uni.request({
				    url: getApp().globalData.doctorURL, 
						method: 'POST',
						header: {
							'content-type': 'application/x-www-form-urlencoded'
						},
				    data: {
							method: 'room_msg_list',
				      room_id: getApp().globalData.roomId, 						//房间号
							uid: getApp().globalData.uid,									//自己的聊天id
							current_msg_id: '',
							direction: '',
							page: '1',
							pageSize: '15',
							timestamp: getApp().globalData.globalTimestamp,
							sign: md5('room_msg_list' + getApp().globalData.globalTimestamp)
				    },
				    
				    success: (res) => {
							if(res.data.code === 200){
								// console.log(res.data.msg)
								if(res.data.msg.length){
									console.log('获取聊天列表成功');
									this.room_list = res.data.msg
									this.$nextTick(() => {
										//进入页面滚动到底部
										this.scrollToId = 'msg' + this.room_list[this.room_list.length - 1].id
									});
								}
							}	
				    },
						fail: (res) => {
							console.log('请求失败哦')
						}
				});
			},
			createSocket () {
				//当前用户
				var sendData = {
					type: "login",
					uid: getApp().globalData.uid,
					client_name: "1",
					room_id: getApp().globalData.roomId					
				}
				uni.connectSocket({
					url: getApp().globalData.socketURL,				//注意：接口成功不意味着链接就打开了
					success: () => {					//接口调用成功
						uni.onSocketOpen( (res) => {					//监听链接是否打开
						  console.log('WebSocket连接已打开！');
							uni.sendSocketMessage({
								data: JSON.stringify(sendData),
								success: () => {
									console.log('成功了。。。。。')
									uni.onSocketMessage( (res) => {
									  console.log('收到服务器内容：');	//String类型
										// console.log(res.data)
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
			sendMsg(type, content){
				let msg = ''
				switch(type){
					case 1: msg = `<div>${this.msg}</div>`;break;
					case 4: msg = content.textValue;break;
					case 5: msg = JSON.stringify(content)
				}
				//发送请求
				uni.request({
					url: getApp().globalData.doctorURL,
					method:'POST',
					header: {
						'content-type': 'application/x-www-form-urlencoded'
					},
					data: {
						method: 'room_msg_add',
						timestamp: getApp().globalData.globalTimestamp, 
						uid:getApp().globalData.uid,
						sign: md5('room_msg_add' + getApp().globalData.globalTimestamp),
						content: msg,
						extra: Date.now(),
						size:'',
						msg_type: String(type),
						room_id: getApp().globalData.roomId,
					},
					success: (res) => {
						console.log('发送消息成功')
						console.log(msg, type)
						console.log(res)
						
						//清空输入框
						this.msg = ''
					},
					fail: () => {
						console.log('发送消息失败')
					}
				})
			},
			handleEmotion(i) {
				this.msg += i
			},
			emotion(res) {
				//console.log('mess', res)
				// console.log('why? res= ' + res)
				let word = res.replace(/\#|\;/gi, '')
				// console.log('word= ' + word)
				const list = ['微笑', '撇嘴', '色', '发呆', '得意', '流泪', '害羞', '闭嘴', '睡', '大哭', '尴尬', '发怒', '调皮', '呲牙', '惊讶', '难过', '酷',
					'冷汗', '抓狂', '吐', '偷笑', '可爱', '白眼', '傲慢', '饥饿', '困', '惊恐', '流汗', '憨笑', '大兵', '奋斗', '咒骂', '疑问', '嘘', '晕', '折磨', '衰',
					'骷髅', '敲打', '再见', '擦汗', '抠鼻', '鼓掌', '糗大了', '坏笑', '左哼哼', '右哼哼', '哈欠', '鄙视', '委屈', '快哭了', '阴险', '亲亲', '吓', '可怜',
					'菜刀', '西瓜', '啤酒', '篮球', '乒乓', '咖啡', '饭', '猪头', '玫瑰', '凋谢', '示爱', '爱心', '心碎', '蛋糕', '闪电', '炸弹', '刀', '足球', '瓢虫',
					'便便', '月亮', '太阳', '礼物', '拥抱', '强', '弱', '握手', '胜利', '抱拳', '勾引', '拳头', '差劲', '爱你', 'NO', 'OK', '爱情', '飞吻', '跳跳',
					'发抖', '怄火', '转圈', '磕头', '回头', '跳绳', '挥手', '激动', '街舞', '献吻', '左太极', '右太极'
				]
				let index = list.indexOf(word)
				return `<img src="https://res.wx.qq.com/mpres/htmledition/images/icon/emotion/${index}.gif" style="width: 24px;height: 24px;vertical-align: middle!important;">`
			},
			chooseImage () {
				//选择图片
				uni.chooseImage({
					count: 1,
					sizeType: ['compressed'],
					sourceType: ['album'],
					success: (res) => {
						console.log('选择图片成功：', res.tempFilePaths[0])
						var imageSrc = {textValue:res.tempFilePaths[0]}
						//上传文件
						this.uploadFile(imageSrc, 4)
					},
					fail: (err) => {
						console.log('chooseImage fail', err)
						// #ifdef MP
						uni.getSetting({
							success: (res) => {
								let authStatus = res.authSetting['scope.album'];
								if (!authStatus) {
									uni.showModal({
										title: '授权失败',
										content: 'Hello uni-app需要从您的相册获取图片，请在设置界面打开相关权限',
										success: (res) => {
											if (res.confirm) {
												uni.openSetting()
											}
										}
									})
								}
							}
						})
						// #endif
					}
				})
			},
			chooseFile () {
				uni.chooseMessageFile({
				  count: 10,
				  type: 'file',
				  success: (res) =>  {
				   console.log(res)
				    for(let i=0;i<res.tempFiles.length;i++){
					   res.tempFiles[i].size  = (res.tempFiles[i].size/1024) ;
					   console.log('fileSize:',res.tempFiles[i].size)
					   res.tempFiles[i].size = Math.trunc(res.tempFiles[i].size);
					   console.log('fileSize:',res.tempFiles[i].size)
				   	let msg = {name:res.tempFiles[i].name,textValue:res.tempFiles[i].path,size:res.tempFiles[i].size};
				   	console.log(msg)
				   	this.uploadFile(msg,5)
				   }
				  }
				})
				console.log('文件上传')
			},
			uploadFile (fileSrc, type) {
				console.log(fileSrc.textValue)
				uni.uploadFile({
					url: getApp().globalData.doctorURL,
					filePath: fileSrc.textValue,
					name: 'file',
					formData: {
						method: 'upload',
						timestamp: getApp().globalData.globalTimestamp, 
						sign: md5('upload' + getApp().globalData.globalTimestamp),
						file: fileSrc.textValue
					},
					success: (res) => {
						console.log(res)
						let tmpres=JSON.parse(res.data);
						// console.log(tmpres)
						let url = 'http://gdoctor.xazhima.com'
						let msg = type === 4 ? {textValue:url+ tmpres.msg.file_path} : {name:fileSrc.name,size:fileSrc.size,textValue: url+tmpres.msg.file_path}
						this.sendMsg(type, msg)
						console.log(msg)
						uni.showToast({
							title: '上传成功',
							icon: 'success',
							duration: 1000
						})
					},
					fail: (err) => {
						console.log('uploadImage fail', err);
						uni.showModal({
							content: err.errMsg,
							showCancel: false
						});
					}
				});
			},
			// 打开文件
			openFile(content){
				content = JSON.parse(content)
				console.log(content)
				uni.downloadFile({
					url: content.textValue,
					success: function(res) {
						var filePath = res.tempFilePath;
						//打开文件有效值 doc, xls, ppt, pdf, docx, xlsx, pptx
						uni.openDocument({
							filePath:filePath,
							success: function(res) {
								console.log(res)
								//that.downloadFile_onoff = true;
							},
							fail(res) {
								console.log(res)
								uni.showToast({
									title: '暂不支持此类型',
									icon:'none',
									duration: 2000
								});
								//uni.hideLoading();
								//that.downloadFile_onoff = true;
							}
						}); 
						
						}
					});
			},
			previewImage (src){
				wx.previewImage({
					current: src, // 当前显示图片的http链接
					urls: [src] // 需要预览的图片http链接列表,至预览当前图片
				})
			},
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
		border-radius: 4px;
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
		min-height: 26px;
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
		align-items: flex-end;
		justify-content: space-around;
		/* height: 50px; */
		padding: 5px 0;
		background-color: #f8f8f8;
	}
	/*  */
	.send-msg-bar ::after{
		content:"";
		display: block; 
		clear:both; 
	}
	.send-edit {
		overflow: scroll;
		font-size: 15px;
		width: 600rpx;
		padding: 5px;
		min-height: 36px;
		max-height: 90px;
		line-height: 26px;
		background-color: #ffffff;
		border-radius: 6px;
		box-sizing: border-box;
	}
	.send-icon {
		font-size: 26px;
		line-height: 33px;
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
	.msg-plus {
		display: flex;
		flex-direction: row;

		height: 200px;
		width: 100%;
		background-color: #ededed;
	}
	
	.other {
		width: 60px;
		height: 60px;
		border-radius: 10px;
		background-color: #FFFFFF;
		margin: 15px;
		font-size: 35px;
		text-align: center;
		line-height: 60px;
	}
</style>
