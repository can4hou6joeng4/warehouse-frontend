<template>
	<view>
		<view class="statistics">
			<image src="../../static/big-icon-1.png" class="big-icon" mode="widthFix"></image>
			<view class="report-title">
				<text class="days">{{sum}}</text>
				<text class="unit">天</text>
			</view>
			<view class="sub-title">
				<text>本月签到</text>
			</view>
			<view class="report">
				<view class="column green">
					<text class="column-title">正常签到</text>
					<text class="number">{{sum_1}}</text>
				</view>
				<view class="column orange">
					<text class="column-title">迟到签到</text>
					<text class="number">{{sum_2}}</text>
				</view>
				<view class="column red">
					<text class="column-title">缺勤签到</text>
					<text class="number">{{sum_3}}</text>
				</view>
			</view>
		</view>
		<view class="calendar-container">
			<uni-calendar :insert="true" :lunar="false" :selected="list" 
			@monthSwitch="changeMonth" @confirm="changeMonth"></uni-calendar>
		</view>
	</view>
</template>

<script>
	import uniCalendar from '@/components/uni-calendar/uni-calendar.vue';
	export default {
		components: {
			uniCalendar
		},
		data() {
			return {
				list: [],
				sum_1: 0,
				sum_2: 0,
				sum_3: 0,
				sum: 0,
			}
		},
		onShow:function(){
			let that=this
			let date=new Date()
			let year=date.getFullYear()
			let month=date.getMonth()+1
			that.searchCheckin(that,year,month)
		},
		methods: {
			searchCheckin:function(ref,year,month){
				let that=ref
				that.sum_1=0
				that.sum_2=0
				that.sum_3=0
				that.sum=0
				that.list.length=0
				
				// 查询本月考勤情况的方法
				that.ajax(that.url.searchMonthCheckin,"GET",{year:year,month:month},function(resp){
					console.log(resp)
					let result = resp.data.data
					for(let one of result.list){
						if(one.status!=null&&one.status!=""){
							let color=""
							if(one.status=="正常"){
								color="green"
							}
							else if(one.status=="迟到"){
								color="orange"
							}
							else if(one.status=="缺勤"){
								color="red"
							}
							else if(one.status=="节假日"){
								color="blue"
							}
							that.list.push({
								date:one.date,
								info:one.status,
								color:color
							})
						}
					}
					that.sum_1=result.sum_1
					that.sum_2=result.sum_2
					that.sum_3=result.sum_3
					that.sum = that.sum_1 + that.sum_2
					console.log(that.sum)
				})
			},
			changeMonth:function(e){
				let that=this
				let year=e.year
				let month=e.month
				that.searchCheckin(that,year,month)
			}
		}
	}
</script>

<style lang="less">
	@import url('my_checkin.less');
</style>
