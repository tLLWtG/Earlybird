<template>
	<view>
		<view class="sentenceContainer">别卷了，快点回去睡觉（请在8:00前完成打卡）</view>
		<view class="dakaSuccessContainer">
			<view>你已经在今日{{hour}}打卡成功</view>
		</view>
		<view v-if="rank!='加载中'" class="dakaSuccessContainer">
			<view>你是今天第{{rank}}个打卡的人</view>
		</view>
		<view v-else class="dakaSuccessContainer">
			<view>排名加载中...</view>
		</view>
		<view v-if="percent!='加载中'" class="dakaSuccessContainer">
			<view>你已经成功击败了{{percent}}%的人</view>
		</view>
		<view v-else class="dakaSuccessContainer">
			<view>百分位加载中...</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				dakaRecords: [],
				hour: '',
				minute: '',
				rank: '加载中',
				percent: '加载中',
				showid: "",
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
			showToast_1() {
				uni.showToast({
					title: '你已经打过卡了'
				})
			},
			showToast_2() {
				uni.showToast({
					title: '打卡成功'
				})
			},
			async update_rank_percen() {
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
				let classSize = uni.getStorageSync('classsize');
				let doneCount = 0;
				let now = new Date();
				let formatdate = this.formatTime(now).substr(0, 10);
				let formattime = this.formatTime(now).substr(11, 5);
				for (let i = 0; i < data.length; i += 1) {
					let record = data[i];
					if (record.date == formatdate && formattime >= record.time)
						++doneCount;
				}
				this.rank = doneCount;
				this.percent = doneCount / classSize * 100;
			},
			async add_record() {
				let now = new Date();
				let formatdate = this.formatTime(now).substr(0, 10);
				let formattime = this.formatTime(now).substr(11, 5);
				const db = uniCloud.database();
				let res = await db.collection('checkrecord').add({
					"id": this.showid,
					"date": formatdate,
					"time": formattime
				});
				console.log(res);
				let isSuccess = res.success;
				let data = res.result.data;
				if (!isSuccess) {
					uni.showToast({
						icon: 'error',
						title: '网络错误！'
					});
					return;
				}
			},
			daka() {
				var flag = 1;
				// 获取当前的日期，用来判断今天是不是已经打过卡了
				var now = new Date();
				var format = this.formatTime(now);
				console.log(format);
				// 先从本地获取打卡的数据
				var dakaRecord = uni.getStorageSync('dakaInfo') || [];
				// 和数组中的日期比较，看看是否已经打过卡了(其实之和数组的第一个元素比也行)
				//因为unshift是在最前面插入，所以最前面的肯定是最新的日期
				for (var i = 0; i < dakaRecord.length; i++) {
					if (format.substr(0, 10) == dakaRecord[i].substr(0, 10)) {
						this.showToast_1();
						flag = 0;
						break;
					}
				}
				//第一次打卡
				if (flag) {
					dakaRecord.unshift(format);
					this.showToast_2();
					this.add_record();
				}
				//将打卡数据传回
				uni.setStorageSync('dakaInfo', dakaRecord);
				if (this.rank == '加载中') {
					this.update_rank_percen();
				}
			}
		},
		onShow() {
			this.daka();
			this.dakaRecords = uni.getStorageSync('dakaInfo');
			this.hour = this.dakaRecords[0].substr(11, 5);
			console.log(this.dakaRecords[0].substr(0, 10));
			this.showid = uni.getStorageSync("showid");
		},
	}
</script>

<style>
	.sentenceContainer {
		border: 2rpx solid #000000;
		border-radius: 10rpx;
		height: 500rpx;
		width: 742rpx;

		line-height: 140rpx;
		text-align: center;
		font-size: 50rpx;
	}

	.dakaSuccessContainer {
		width: 742rpx;
		height: 200rpx;

		margin: 25rpx auto;

		line-height: 200rpx;
		text-align: center;
		font-size: 50rpx;

		border: 2rpx solid black;
		border-radius: 10rpx;
	}
</style>