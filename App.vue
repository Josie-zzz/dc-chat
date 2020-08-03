<script>
	export default {
		globalData: { //全局变量
			globalTimestamp: Date.now().toString(),
			doctorURL: 'http://gdoctor.xazhima.com/api/v1.0/api.php',
			socketURL: 'wss://gdoctor.xazhima.com/wss',
			openId: '',
			session_key: '',
			encryptedData: '',
			iv: '',
			nickName: '',
			avatarUrl: '',
			authIsTrue: false, //表示用户是否已经授权
			roomId: '',
			uid: '',
			
		},
		onLaunch: function() {
			console.log('App Launch')
		},
		onShow: function() {
			console.log('App Show')
		},
		onHide: function() {
			console.log('App Hide')
		}
	}
</script>

<style>
	/*每个页面公共css */
	page {
		width: 100%;
		height: 100%;
	}
	
	.content {
		width: 100%;
		height: 100%;
		background-color: #ededed;
	}
</style>
