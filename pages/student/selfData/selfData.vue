<template>
	<view>
		<view class="dataContainer">
			<view class="dataBlocks" v-for="(item,index) in selfData">
				<view>{{item.text}}</view>
				<view>{{item.val}}</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				selfData: [{
						text: '缺勤次数',
						val: 0
					},
					{
						text: '功德量',
						val: 0
					},
					{
						text: '缺勤率',
						val: 0
					},
					{
						text: '平均早起时间',
						val: 0
					}
				]
			}
		},
		methods: {

		},
		onLoad() {
			//导入求签打卡记录
			var qiuQianRecord = uni.getStorageSync('qiuQianInfo') || [];
			var dakaRecord = uni.getStorageSync('dakaInfo') || [];
			//计算今天是今年的第几天，以及累计打卡天数
			var now = new Date();
			var firstDay = new Date(now.getFullYear(), 0, 1);
			var passedDays = Math.ceil((now - firstDay) / (1000 * 60 * 60 * 24));
			var dakaDays = dakaRecord.length;
			//修改缺勤次数，功德量和缺勤率
			this.selfData[0].val = passedDays - dakaDays;
			this.selfData[1].val = qiuQianRecord.length;
			this.selfData[2].val = (((passedDays - dakaDays) * 100 / passedDays).toFixed(1)).toString() + '%';
			//计算平均早起时间
			var averageTime = 0;
			console.log(dakaRecord[0].substr(11, 1));
			for (var i = 0; i < dakaRecord.length; i++) {
				var hour = parseInt(dakaRecord[i].substr(11, 1)) * 10 + parseInt(dakaRecord[i].substr(12, 1));
				var minute = parseInt(dakaRecord[i].substr(14, 1)) * 10 + parseInt(dakaRecord[i].substr(15, 1));
				averageTime += hour * 60 + minute;
			}
			var averageHour = Math.floor(averageTime / 60);
			var averageMinute = averageTime - averageHour * 60;
			this.selfData[3].val = averageHour.toString() + ':' + averageMinute.toString();

		}
	}
</script>

<style>
	.dataContainer {
		display: flex;
		justify-content: space-around;
		flex-wrap: wrap;

		width: 742rpx;
		height: 500rpx;

		border: 2rpx solid #000000;
		border-radius: 10rpx;
	}

	.dataContainer .dataBlocks {
		width: 340rpx;
		height: 200rpx;

		margin: 30rpx auto;
		border-radius: 10rpx;
	
		line-height: 100rpx;
		text-align: center;
		font-size: 40rpx;
	}
</style>