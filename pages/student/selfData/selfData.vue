<template>
	<view>
		<view class="subtitle">
			过去七天历史记录统计
		</view>
		<view class="dataContainer">

			<view class="dataBlocks" v-for="(item,index) in selfData">
				<view>{{item.text}}</view>
				<view>{{item.val}}</view>
			</view>
		</view>
		<view class="subtitle" style="text-align: left;">
			完整历史记录
		</view>
		<view v-for="(item,index) in recordarr" :key="index">
			<view>{{item.date}}</view>
			<view>{{item.time}}</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				selfData: [{
						text: '缺勤次数',
						val: '加载中...'
					},
					{
						text: '打卡次数',
						val: '加载中...'
					},
					{
						text: '缺勤率',
						val: '加载中...'
					},
					{
						text: '平均早起时间',
						val: '加载中...'
					}
				],
				recordarr: [],
				showid: ""
			}
		},
		methods: {
			formatTime(date) {
				const year = date.getFullYear();
				const month = String(date.getMonth() + 1).padStart(2, '0');
				const day = String(date.getDate()).padStart(2, '0');
				const hours = String(date.getHours()).padStart(2, '0');
				const minutes = String(date.getMinutes()).padStart(2, '0');
				return `${year}-${month}-${day} ${hours}:${minutes}`;
			},
			async updateRecord() {
				const db = uniCloud.database();
				let res = await db.collection('checkrecord').get();
				let isSuccess = res.success;
				let data = res.result.data;
				if (!isSuccess) {
					uni.showToast({
						icon: 'error',
						title: '网络错误！'
					});
					return;
				}
				console.log(data);
				this.recordarr = [];
				for (let i = 0; i < data.length; i += 1) {
					let record = data[i];
					if (record.id == this.showid)
						this.recordarr.push(record);
				}
				// let now = new Date();
				// let formatdate = this.formatTime(now).substr(0, 10);

				const currentDate = new Date();
				let totalTime = 0;
				// 统计过去七天的日期范围
				const sevenDaysAgo = new Date(currentDate);
				sevenDaysAgo.setDate(currentDate.getDate() - 6); // 减去六天，因为包括当前日期总共七天

				// 过滤出在过去七天范围内的数据
				const filteredData = this.recordarr.filter(entry => {
					const entryDate = new Date(entry.date);
					return entryDate >= sevenDaysAgo && entryDate <= currentDate;
				});
				for (let i = 0; i < filteredData.length; ++i) {
					const time = filteredData[i].time.split(":").map(Number); // 将时间字符串转换为 [hours, minutes]
					const totalMinutes = time[0] * 60 + time[1];
					totalTime += totalMinutes;
				}
				const averageTimeInMinutes = totalTime / filteredData.length;
				const hours = Math.floor(averageTimeInMinutes / 60);
				const minutes = Math.round(averageTimeInMinutes % 60);
				const formattedAverageTime =
					`${hours.toString().padStart(2, '0')}:${minutes.toString().padStart(2, '0')}`;

				console.log(filteredData);
				this.selfData[0].val = 7 - filteredData.length;
				this.selfData[1].val = filteredData.length;
				this.selfData[2].val = (this.selfData[0].val / 7 * 100).toFixed(0) + "%";
				this.selfData[3].val = formattedAverageTime;
				this.recordarr = this.recordarr.reverse();
			}
		},
		onShow() {
			this.showid = uni.getStorageSync("showid");
			this.updateRecord();
		}
	}
</script>

<style>
	.subtitle {
		width: 650rpx;
		height: 150rpx;

		margin: 50rpx auto;

		line-height: 50rpx;
		font-size: 30rpx;
		text-align: center;
	}

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