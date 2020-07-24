<template>
	<!-- 模仿微信的一个底部发送框 -->
	<view class="send-msg-bar">	
		<!-- 发语音 -->
		<text class="send-voice iconfont send-icon left">&#xe68d;</text>
		<!-- 转换键盘 -->
		<text class="send-keyBoard iconfont send-icon left" style="display: none;">&#xe65b;</text>
		<textarea class="send-edit" v-model="msg"></textarea>
		<view class="right">
			<!-- 发表情 -->
			<text class="send-emoji iconfont send-icon">&#xe609;</text>
			<!-- 添加多选 -->
			<text class="send-add iconfont send-icon" v-show="msg === ''">&#xe631;</text>
			<!-- 发送按钮 -->
			<button class="send-btn" v-show="msg !== ''">发送</button>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				msg: '',	//储存当前输入的信息 
			};
		},
		methods: {
			
		},
		computed: {
			
		}
	}
</script>

<style>
	.send-msg-bar {
		width: 100%;
		display: flex;
		flex-direction: row;
		align-items: center;
		justify-content: space-around;
		height: 50px;
		background-color: #f8f8f8;
		
		position: fixed;
		bottom: 0;
		left:0;
	}
	.send-edit {
		font-size: 15px;
		/* width: 80px; */
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
	.left, .right{
		margin: 0 10px;
	}
	.right {
		display: flex;
		flex-direction: row;
		align-items: center;
	}
	.right .send-emoji {
		margin-right: 10px;
	}
</style>
