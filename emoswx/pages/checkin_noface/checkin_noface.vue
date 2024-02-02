<template>
	<view>
		<view class="checkin-top">
			<view class="image-box">
				<image src="../../static/checkin.png" style="width: 100%;height: 100%;"></image>
			</view>
			<view style="margin-top: 10rpx;" :style="{ color: status === '当前可以考勤' ? '#6a9335' : 'red' }">
				{{status}}
			</view>
			<view style="font-size: 16px;">
				广州佑安
			</view>
		</view>
		<view class="checkin-container" @tap="checkin">
			<view class="content">
				<view style="font-size: 20px;font-weight: 800;font-family: cursive;">上班打卡</view>
				<view style="font-size: 20px;font-weight: 800;font-family: cursive;">{{currentTime}}</view>
				<view style="font-size: 12px;color: #bababa;">请{{attendanceTime}}前打卡</view>
			</view>			
		</view>
		<!-- <view style="font-size: 12px;">当前所在位置：广东省广州市天河区</view> -->
			
		<view class="checkin-schedule">
			<view>上班时间：{{attendanceTime}}</view>
			<view>上班打卡开始时间：{{attendanceStartTime}}</view>
			<view>上班打卡结束时间：{{attendanceEndTime}}</view>
		</view>
<!-- 		<view style="width: 40px;height: 40px;background-color: red;" @tap="getlocation">
			123
		</view> -->
	</view>
</template>

<script>
import utils from '@/utils/utils.js';

var QQMapWX = require('../../lib/qqmap-wx-jssdk.min.js');
var qqmapsdk;
export default {
	data() {
		return {
			canCheckin: true, // 判断是否可以考勤
			photoPath: '',
			btnText: '拍照',
			showCamera: true,
			showImage: false,
			status: "", // 是否可以考勤
			attendanceEndTime:"", // 打卡结束时间
			attendanceStartTime:"", // 打卡开始时间
			attendanceTime:"", // 上班时间
			currentTime:""
		};
	},
	onShow: function() {
		let that = this;
		that.updateClock()
		// 判断当前时间是否可以考勤
		that.ajax(that.url.validCanCheckIn, 'GET', null, function(resp) {
			utils.logging('查看是否可以考勤', resp);
			let data = resp.data.data;
			that.status = data.status
			that.attendanceEndTime = data.attendanceEndTime
			that.attendanceStartTime = data.attendanceStartTime
			that.attendanceTime = data.attendanceTime
			
			if (data.status != '可以考勤') {
				that.canCheckin = false;
				setTimeout(function() {
					uni.showToast({
						title: data.status,
						icon: 'none'
					});
				}, 1000);
			} else {
				setTimeout(function() {
					uni.showToast({
						title: data.status
					});
				}, 1000);
			}
		});
	},
	methods: {
		// 时钟
		updateClock: function () {
			const that = this; // 保存页面引用以便后续使用

			// 更新时间并在页面上显示
			function update() {
			  const now = new Date();
			  const hours = now.getHours().toString().padStart(2, '0');
			  const minutes = now.getMinutes().toString().padStart(2, '0');
			  const seconds = now.getSeconds().toString().padStart(2, '0');

			  const timeString = `${hours}:${minutes}:${seconds}`;

			  that.currentTime = timeString

			}

			update(); // 立即显示时间

			// 每秒更新一次时间
			setInterval(() => {
			  update();
			}, 1000);
		},
	
		checkin: function () {
			const that = this;
			if(that.status == "当前可以考勤"){
				uni.getLocation({
					type: 'gcj02', // 返回可以用于uni.openLocation的坐标
					isHighAccuracy: true,
					success: function (res) {
						console.log('Latitude: ' + res.latitude + ', Longitude: ' + res.longitude);
						// 可以将 res.longitude 和 res.latitude 用于地图展示等
						wx.request({
							url: 'https://apis.map.qq.com/ws/geocoder/v1/',
							method: 'GET',
							data: {
							  location: `${res.latitude},${res.longitude}`,
							  key: 'SFZBZ-YWM3Z-YYAXA-TWWBE-BZCNE-5ZBEO'
							},
							success: (res) => {
								console.log(res)
								that.toCheckin(res.data.result.address);
							}
						})
					}, 
					fail: function (error) {
						console.error('Location acquisition failed: ' + JSON.stringify(error));
					},
					complete: function () {
						console.log('getLocation operation is complete');
					}
				});
			}else{
				setTimeout(function() {
					uni.showToast({
						title: that.status
					});
				}, 1000);
			}
		},
		
		// 签到具体方法
		toCheckin: function (address) {
			const that = this;
			
			console.log(address)
			let data = {}
			data.address = address
			that.ajax(that.url.checkinNoFace, 'POST', data, function(resp) {
				let result = resp.data
				console.log(resp)
				if (resp.statusCode == 200){
					switch(result.code){
						case(500):{
							uni.showToast({
								title: result.message,
								icon: 'none'
							});
						};break;
						case(200):{
							uni.showToast({
								title: '签到成功',
								complete: function() {
									uni.navigateTo({
										url: '../checkin_result/checkin_result'
									});
								}
							});
						};break;
					}
				}else{
					uni.showToast({
						title: "系统错误！",
						icon: 'none'
					});
				}
			})
		}
	}
};
</script>

<style lang="less">
@import url('checkin_noface.less');
</style>
