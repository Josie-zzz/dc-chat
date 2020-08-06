<template>
	<view class="content">
		<view class="doc-info">
			<image :src="bigheadimg" mode="aspectFill"></image>
			<view class="info-text">
				<view class="name">
					{{name}}  {{title}}
				</view>
				<view class="desc">
					{{desc}}
				</view>
			</view>
		</view>
		<view class="uni-calendar">
			<UniCalendar
			v-show="false"
			:class="{isShowBox:isShowBox}"
			class="calendar"
			:insert="true"
			:selected="dateArrList"
			@change="change"
			 />
			<!-- 预约时间选择框	 -->
			<view v-if="isShowBox" class="choose-date">
				<view class="aTable" >
					<view class="nowTime">
						出诊时间 {{ selectedTime }} 
					</view>
					<view class="closeBox" @click="() => {isShowBox = false}">返回</view>
					<view class="table">
						<view class="header">
							<view class="tr">
								<view class="td1 td">时间段</view>
								<view class="td2 td">预约费用</view>
								<view class="td3 td">操作</view>
							</view>
						</view>
						<view class="body">
							
							<template v-if="timeList.length">
								<block v-for="(time, index) in timeList" :key="time.id">
									<view class="tr">
										<view class="td1 td">{{ time.starttime }} -- {{ time.endtime }}</view>
										<view class="td2 td">{{time.money}}</view>
										<view class="td3 td">
											<view class="txt" ref="timeInfo" @click="openComInfro(index)">确定预约</view>
										</view>
									</view>
								</block>
							</template>
							
							<!-- 确定预约信息弹框 -->
							<uni-popup ref="popup" type="dialog">
							    <uni-popup-dialog type="input" mode="base" title="确定预约" :content="popContent1" :duration="2000" :before-close="true" @close="close" @confirm="confirm"></uni-popup-dialog>
							</uni-popup>
						</view>
					</view>
				</view>
			</view>
			
		</view>
	</view>
</template>

<script>
	import UniCalendar from '@/components/uni-calendar/uni-calendar.vue'
	import uniPopup from '@/components/uni-popup/uni-popup.vue'
	import uniPopupMessage from '@/components/uni-popup/uni-popup-message.vue'
	import uniPopupDialog from '@/components/uni-popup/uni-popup-dialog.vue'
	var md5 = require("../../common/md5.js");
	
	export default {
		components: {
			UniCalendar,
			uniPopup,
			uniPopupMessage,
			uniPopupDialog
		},
		data() {
			return {
				dateArrList: [],			//储存医生预约的数组
				bigheadimg: '',  			//医生信息描述的头像
				name: '',							//医生名字
				title: '',						//医生头衔
				desc: '',							//医生描述
				isShowBox: false,			//是否显示预约具体信息
				timeList: null,				//某一天的时间预约信息，临时的存储,如果没有预约会被设置为null
				selTime: '',					//当前被选中的时间
				popContent1: '',  		//确定预约的确认信息
			};
		},
		computed: {
			selectedTime(){
				if(this.selTime){
					let time = this.selTime.split('-')
					return `${time[0]}年${time[1]}月${time[2]}日`
				}
			}
		},
		onLoad() {
			//获取医生信息
			this.getDocAppoint()
			//获取医生当前月预约时间
			this.getDocDateMonth()
		},
		methods: {
			open(){
				this.$refs.popup.open()
			},
			close(done){
				done()
			},
			confirm(done,value){
				// 输入框的值
				console.log(value)
				done()
			},
			openComInfro(index){			//点击确定预约
				let str = (`
					<div>预约上医:${this.name}</div>
					<div>预约时间:${this.selectedTime} ${this.timeList[index].starttime} - ${this.timeList[index].endtime} </div>
					<div>预约费用:${this.timeList[index].money}</div>
				`)	
				this.popContent1 = str
				this.open()
				// console.log(index)
			},
			async change(e) {
				// console.log(e);
				//此日期有预约时间
				if(e.extraInfo.date){
					this.selTime = e.extraInfo.date
					console.log('预约')
					await this.getDocDateDay(e.extraInfo.date)
					this.isShowBox = true
				} else {
					this.timeList = null
				}
				// console.log(this.timeList)
			},
			getDocAppoint () {	
				//获取医生预约信息
				uni.request({
					url: getApp().globalData.doctorURL,
					method:'POST',
					header: {
						'content-type': 'application/x-www-form-urlencoded'
					},
					data: {
						method: 'get_mydoctor_info',
						timestamp: getApp().globalData.globalTimestamp, 
						sign: md5('get_mydoctor_info' + getApp().globalData.globalTimestamp),
						openId: getApp().globalData.openId
					},
					success: (res) => {
						// console.log(res)
						if(res.data.code === 200){
							console.log('获取医生信息成功');
							// console.log(res.data.msg)
							this.bigheadimg = 'http://gdoctor.xazhima.com' + res.data.msg.bigheadimg
							this.name = res.data.msg.name
							this.title = res.data.msg.title
							this.desc = res.data.msg.desc
						}
					},
					fail: () => {
						console.log('医生信息获取失败')
					}
				})
			},
			getDocDateMonth() {
				//获取医生当前月的预约时间
				uni.request({
					url: getApp().globalData.doctorURL,
					method:'POST',
					header: {
						'content-type': 'application/x-www-form-urlencoded'
					},
					data: {
						method: 'get_schedule_day_list',
						timestamp: getApp().globalData.globalTimestamp, 
						sign: md5('get_schedule_day_list' + getApp().globalData.globalTimestamp),
						openId: getApp().globalData.openId,
						year: new Date().getUTCFullYear(),	
						month: new Date().getUTCMonth() + 1	
					},
					success: (res) => {
						// console.log(res)
						if(res.data.code === 200){
							console.log('获取医生当前月预约时间成功');
							// console.log(res.data.msg.ok)
							if(res.data.msg.ok.length){
								res.data.msg.ok.forEach((value) => {
									let dateArr = null		//储存某一天的标点信息，显示在日历上面
									dateArr = {
										date: value,
										info: '预约', 
									}
									this.dateArrList.push(dateArr)
								})
							}
						}
					},
					fail: () => {
						console.log('医生当前月预约时间获取失败')
					}
				})
			},
			async getDocDateDay(date){
				//获取医生某一天预约具体时间
				var [error, res] = await uni.request({
					url: getApp().globalData.doctorURL,
					method:'POST',
					header: {
						'content-type': 'application/x-www-form-urlencoded'
					},
					data: {
						method: 'get_schedule_list',
						timestamp: getApp().globalData.globalTimestamp, 
						sign: md5('get_schedule_list' + getApp().globalData.globalTimestamp),
						openId: getApp().globalData.openId,
						date: date
					}
				})
				// console.log(res.data);
				if(res.data.code === 200){
					console.log('获取医生某一天预约时间成功');
					if(res.data.msg.length){
						// console.log('res.data.msg', res.data.msg)
						//处理里面时间的格式
						res.data.msg.forEach(val => {
							val.starttime = this.transTime(val.starttime)
							val.endtime = this.transTime(val.endtime)
						})
						this.timeList = res.data.msg
						console.log(this.timeList)
					}
				}
			},
			transTime(time){
				//转换成Number类型的毫秒数
				time = parseInt(time +'000')
				var date = new Date()
				date.setTime(time) // date对象 的值就变成这样的格式 Mon Aug 03 2020 04:20:00 GMT+0800
				var hour = date.getHours();
				var min = date.getMinutes();
				hour = hour < 10 ? ('0' + hour) : hour
				min = min < 10 ? ('0' + min) : min
				return `${hour}:${min}`
				// console.log(time)
			}
		}
	};
</script>

<style scoped>
	/* 因为v-show不接受template,所以只能这样 */
	.isShowBox {
		display: none;
	}
	.content {
		display: flex;
		flex-direction: column;
		align-items: center;
	}
	.doc-info {
		height: 480rpx;
	}
	.doc-info image {
		width: 100%;
		height: 50%;
		border-top-right-radius: 20rpx;
		border-top-left-radius: 20rpx;
	}
	.doc-info, .uni-calendar{
		width: 680rpx;
		border-radius: 20rpx;
		background-color: #FFFFFF;
		margin-top: 50rpx;
	}
	.calendar {
		width: 100%;
		height: 100%;
	}
	.choose-date {
		width: 100%;
		height: 480rpx;
		border-radius: 20rpx;
		background-color: #FFFFFF;
	}
	.info-text {
		padding: 25rpx;
		box-sizing: border-box;
		height: 50%;
	}
	.info-text .name {
		font-size: 18px;
		font-weight: 600;
		margin-bottom: 14rpx;
	}
	.info-text .desc {
		font-size: 14px;
		height: 135rpx;
		width: 100%;
		overflow: scroll;

	}
	/* 显示时间表的 */
	.aTable {
		display: flex;
		position: relative;
		flex-direction: column;
		width: 100%;
		height: 100%;
	}
	.nowTime {
		font-size: 16px;
		line-height: 80rpx;
		text-align: center;
		font-weight: 600;
	}
	.closeBox {
		position: absolute;
		top: 10rpx;
		right: 20rpx;
		font-size: 30rpx;
		line-height: 60rpx;
		text-align: center;
		color: #bf2924;
	}
	.table {
		flex: 1;
		border-top: #000 solid 2rpx;
		overflow: scroll;
	}
	.header {
		position: absolute;
		width: 100%;
		top: 80rpx;
		left: 0;
		background-color: #fff;
	}
	.body {
		padding-top: 68rpx;
	}
	.tr {
		display: flex;
		flex-direction: row;
		width: 100%;
	}
	.td {
		margin: 14rpx 0;
		text-align: center;
		font-size: 14px;
		color: #6b6b6b;
		line-height: 40rpx;
	}
	.header .td {
		font-size: 16px;
	}
	.body .td1, .body .td2 {
		font-size: 12px;
	}
	.td1, .td3 {
		width: 35%;
	}
	.td2 {
		width: 30%;
	}
	.body .td3 .txt {
		display: inline-block;
		width: 60%;
		background-color: #bf2924;
		color: #edc4c3;
		border-radius: 20rpx;
	}
	
</style>
