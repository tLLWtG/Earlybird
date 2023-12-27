<template>
	<view>
		<view class="headContainer">打卡排名</view>
		<view class="">
			您的打卡记录
		</view>
		<view style="margin: 15rpx auto;display: flex; width: 100%; justify-content: center;">
			<view class="topDataContainer" style="justify-content: center;">
				<view style="display: flex; justify-content: space-between; width: 400rpx;">
					<view>{{usersInfo[0].userName}}</view>
					<view>{{usersInfo[0].dakaTime}}</view>
				</view>
			</view>
		</view>

		<view style="display: flex; justify-content: center;">
			<view style="display: flex; justify-content: space-between; width: 700rpx;">
				<text>名次</text>
				<text>姓名</text>
				<text>打卡时间</text>
			</view>
		</view>

		<view class="dataContainer" v-for="(item,index) in usersInfo" v-if="index">
			<view>{{index}}</view>
			<view>{{item.userName}}</view>
			<view>{{item.dakaTime}}</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				showid: "",
				usersInfo: [] //用来存储各个用户的打卡信息
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
			async getAllRecord() {
				this.showid = uni.getStorageSync("showid");
				const db = uniCloud.database();
				let res = await db.collection('checkrecord').limit(1000).get();
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
				let now = new Date();
				let formatdate = this.formatTime(now).substr(0, 10);
				console.log(data);
				this.usersInfo = [];
				for (let i = 0; i < data.length; i += 1) {
					let record = data[i];
					if (record.date == formatdate) {
						this.usersInfo.push({
							"userName": record.name,
							"dakaTime": record.time
						});
						if (record.id == this.showid) {
							this.usersInfo.unshift({
								"userName": record.name,
								"dakaTime": record.time
							});
						}
					}
				}
			}
		},
		onShow() {
			this.getAllRecord();
			// 加载页面之前，对所有用户信息的打卡时间进行排序，把排名最高的放在前面
			// this.usersInfo.sort(function(a, b) {
			// 	var hour_a = a.dakaTime.substr(0, 1) * 10 + a.dakaTime.substr(1, 1);
			// 	var minute_a = a.dakaTime.substr(3, 1) * 10 + a.dakaTime.substr(4, 1);
			// 	var hour_b = b.dakaTime.substr(0, 1) * 10 + b.dakaTime.substr(1, 1);
			// 	var minute_b = b.dakaTime.substr(3, 1) * 10 + b.dakaTime.substr(4, 1);
			// 	hour_a = parseInt(hour_a);
			// 	minute_a = parseInt(minute_a);
			// 	hour_b = parseInt(hour_b);
			// 	minute_b = parseInt(minute_b);
			// 	return hour_a * 60 + minute_a - hour_b * 60 - minute_b;
			// })
		}
	}
</script>

<style>
	.headContainer {
		width: 742rpx;
		height: 350rpx;


		line-height: 350rpx;
		text-align: center;
		font-size: 50rpx;
	}

	.dataContainer {
		width: 650rpx;
		height: 150rpx;

		border: 2rpx solid black;
		border-radius: 10rpx;

		line-height: 150rpx;
		text-align: center;
		font-size: 40rpx;

		padding: 0 30rpx;
		margin: 15rpx auto;

		display: flex;
		justify-content: space-between;
	}

	.topDataContainer {
		width: 650rpx;
		height: 150rpx;

		border: 2rpx solid black;
		border-radius: 10rpx;

		line-height: 150rpx;
		text-align: center;
		font-size: 40rpx;

		margin-bottom: 50rpx;
		padding: 0 30rpx;
		background-color: #F5F5DC;

		display: flex;
		justify-content: space-between;
	}
</style>