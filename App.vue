<script>
	export default {
		globalData: { //全局变量
			globalTimestamp: Date.now().toString()
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
