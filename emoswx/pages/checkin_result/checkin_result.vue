<template>
	<view class="page">
		
		<!-- 个人信息部分 start -->
		<view class="summary-container">
			<view class="user-info">
				<view class="info">
					<text class="username">当前用户：{{userName}}</text>
				</view>
			</view>
			<view class="date">签到时间：{{checkinTime}}</view>
		</view>
		
		<!-- 个人信息部分 end -->
		
		<!-- 当天的签到信息部分 start -->
		<view class="result-container">
			<view class="left">
				<image src="../../static/icon-6.png" mode="widthFix" class="icon-timer"></image>
				<image src="../../static/icon-6.png" mode="widthFix" class="icon-timer"></image>
				<view class="line"></view>
			</view>
			<view class="right">
				<view class="row">
					<text class="start">上班（时间 {{ attendanceTime }}）</text>
				</view>
				<view class="row">
					<text class="checkin-result green other" v-if="status=='正常'">{{status}}</text>
					<text class="checkin-result yellow other" v-if="status=='迟到'">{{status}}</text>
				</view>
				<view class="row">
					<image src="../../static/icon-8.png" mode="widthFix" class="icon-small"></image>
					<text class="desc">身份验证</text>
					<text class="checkin-result green">已通过</text>
				</view>
				<view class="row">
					<text class="end">下班（时间 {{ closingTime }}）</text>
				</view>
			</view>
		</view>
		<!-- 当天的签到信息部分 end -->
		
		<!-- 累积签到信息部分 start -->
		<view class="checkin-report">
			<image src="../../static/big-icon-1.png" mode="widthFix" class="big-icon"></image>
			<view class="report-title">
				<text class="days">{{checkinDays}}</text>
				<text class="unit">天</text>
			</view>
			<view class="sub-title">
				<text>累计签到</text>
				<view class="line"></view>
			</view>
			<view class="calendar-container">
				<view class="calendar" v-for="one in weekCheckin" :key="one">
					<image src="../../static/icon-9.png" mode="widthFix" class="calendar-icon" v-if="one.type=='工作日'"></image>
					<image src="../../static/icon-10.png" mode="widthFix" class="calendar-icon" v-if="one.type=='节假日'"></image>
					<text class="day">{{one.day}}</text>
					<text class="result green" v-if="one.status=='正常'">{{one.status}}</text>
					<text class="result yellow" v-if="one.status=='迟到'">{{one.status}}</text>
					<text class="result red" v-if="one.status=='缺勤'">{{one.status}}</text>
				</view>
			</view>
		</view>
		<!-- 累积签到信息部分 end -->
		
	</view>
</template>

<script>
	export default {
		data() {
			return {
				name:"",  // 名字
				photo:"",  // 照片
				deptName:"",  // 部门名字
				address: "",
				status: "",
				risk: "",
				checkinTime: "",
				date: "",  // 签到日期
				attendanceTime: "",
				closingTime: "",
				checkinDays: 0,
				weekCheckin:[],
				userName:""
			}
		},
		onShow:function(){
			let that=this
			that.ajax(that.url.searchTodayCheckin,"GET",null,function(resp){
				console.log(resp)
				let result=resp.data.data
				that.userName=result.user_name
				that.attendanceTime = result.attendanceTime
				that.closingTime = result.closingTime
				// that.status = result.status
				that.checkinTime = result.checkinDateTime
 				that.weekCheckin = result.weekCheckin
				that.checkinDays= result.checkinDays
			})
		},
		methods: {
			
		}
	}
</script>

<style lang="less">
	@import url("checkin_result.less");
</style>
