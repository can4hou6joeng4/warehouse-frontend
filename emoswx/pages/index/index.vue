<template>
	<view class="page">
		<view class="nav-container">
			<view class="nav-row">
				<view class="nav" @tap="toPage('在线签到','../checkin_noface/checkin_noface')">
					<image mode="widthFix" src="../../static/nav-1.png" class="icon"></image>
					<text class="name">在线签到</text>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		components:{},
		data() {
			return {
			}
		},
		onLoad:function() {
		},
		
		methods: {
			toPage:function(name,url){
				let flag=true
				uni.navigateTo({
					url:url
				})
			},
		}
	}
</script>

<style lang="less">
	@import url("index.less");
</style>
