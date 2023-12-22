<template>
	<view>
		<view class="headContainer">
			<view class="dataBlocks" v-for="(item,index) in totalData">
				<view>{{item.text}}</view>
				<view>{{item.val}}</view>
			</view>
		</view>
		<view class="dataContainer" v-for="(item,index) in usersInfo">
			<view>{{index}}</view>
			<view>{{item.uesrName}}</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				totalData: [
					{
						text:'缺勤人数',
						val:'10'
					},
					{
						text:'缺勤率',
						val:'20%'
					}
				],
				usersInfo: [{
						uesrName: '张三',
						dakaTime: '08:11',
					},
					{
						uesrName: '张三',
						dakaTime: '08:15',
					},
					{
						uesrName: '张三',
						dakaTime: '08:10',
					},
					{
						uesrName: '张三',
						dakaTime: '08:08',
					},
					{
						uesrName: '张三',
						dakaTime: '08:11',
					},
					{
						uesrName: '张三',
						dakaTime: '08:15',
					},
					{
						uesrName: '张三',
						dakaTime: '08:10',
					},
					{
						uesrName: '张三',
						dakaTime: '08:08',
					}
				] //用来存储各个用户的打卡信息
			}
		},
		methods: {

		},
		onLoad() {
			// 加载页面之前，对所有用户信息的打卡时间进行排序，把排名最高的放在前面
			this.usersInfo.sort(function(a, b) {
				var hour_a = a.dakaTime.substr(0, 1) * 10 + a.dakaTime.substr(1, 1);
				var minute_a = a.dakaTime.substr(3, 1) * 10 + a.dakaTime.substr(4, 1);
				var hour_b = b.dakaTime.substr(0, 1) * 10 + b.dakaTime.substr(1, 1);
				var minute_b = b.dakaTime.substr(3, 1) * 10 + b.dakaTime.substr(4, 1);
				hour_a = parseInt(hour_a);
				minute_a = parseInt(minute_a);
				hour_b = parseInt(hour_b);
				minute_b = parseInt(minute_b);
				return hour_a * 60 + minute_a - hour_b * 60 - minute_b;
			})
		}
	}
</script>

<style>
	.headContainer {
		display: flex;
		justify-content: space-between;
		
		width: 742rpx;
		height: 350rpx;

		border: 2rpx solid black;
		border-radius: 10rpx;

		line-height: 350rpx;
		text-align: center;
	}

	.dataContainer {
		width: 683rpx;
		height: 150rpx;

		border: 2rpx solid black;
		border-radius: 10rpx;

		line-height: 150rpx;
		text-align: center;

		padding: 0 30rpx;
		margin: 15rpx auto;

		display: flex;
		justify-content: space-between;
	}
	
	.headContainer .dataBlocks {
		width: 340rpx;
		height: 200rpx;
	
		line-height: 100rpx;
		text-align: center;
	}
</style>