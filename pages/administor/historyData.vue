<template>
	<view class="">
		<view style="margin: 30rpx auto; width: 300rpx; text-align: center;">
			过去七天缺勤统计图
		</view>
		<view class="charts-box">
			<qiun-data-charts type="column" :chartData="chartData" />
		</view>
		<view style="margin: 30rpx auto; width: 300rpx; text-align: center;">
			学生缺勤详情
		</view>
		<view class="dataContainer" v-for="(item,index) in quearr">
			<view>{{index}}</view>
			<view>{{item.name}}</view>
			<view>{{item.id}}</view>
			<view>{{item.count}}</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				chartData: {},
				pastDate: [],
				pastVal: [],
				quearr: []
			};
		},
		onShow() {
			this.updateSta();
			setTimeout(function() {
				this.make_chart();
			}.bind(this), 500);
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
			async updateSta() {
				let tdate = new Date();
				let temparr = [];
				tdate.setDate(tdate.getDate() - 6);
				this.pastDate = [];
				this.pastVal = [];
				for (let i = 1; i <= 7; ++i) {
					this.pastDate.push(this.formatTime(tdate).substr(5, 5));
					temparr.push(this.formatTime(tdate).substr(0, 10));
					tdate.setDate(tdate.getDate() + 1);
				}
				console.log(this.pastDate);
				let book = new Map();
				for (let i = 0; i < 7; ++i)
					book.set(temparr[i], 0);
				console.log(book);

				const db = uniCloud.database();
				let res = await db.collection('checkrecord').limit(1000).get();
				let isSuccess = res.success;
				let data = res.result.data;

				const db2 = uniCloud.database();
				let res2 = await db2.collection('login').limit(1000).get();
				let isSuccess2 = res2.success;
				let data2 = res2.result.data;

				if (!isSuccess || !isSuccess2) {
					uni.showToast({
						icon: 'error',
						title: '网络错误！'
					});
					return;
				}
				console.log(data);
				let classSize = uni.getStorageSync('classsize');
				for (let j = 0; j < temparr.length; ++j) {
					let curDate = temparr[j];
					console.log(curDate);
					for (let i = 0; i < data.length; i += 1) {
						let record = data[i];
						if (record.date == curDate)
							book.set(curDate, book.get(curDate) + 1);
					}
				}
				for (let j = 0; j < temparr.length; ++j) {
					let curDate = temparr[j];
					this.pastVal.push(classSize - book.get(curDate));
				}
				console.log(book);
				console.log(this.pastVal);

				book = new Map();
				for (const person of data2)
					book.set(person.id, 0);
				for (const curDate of temparr) {
					for (let record of data) {
						if (record.date == curDate)
							book.set(record.id, book.get(record.id) + 1);
					}
				}
				for (const person of data2) {
					if (!person.admin)
						this.quearr.push({
							'name': person.name,
							'id': person.id,
							'count': 7 - book.get(person.id)
						});
				}
				this.quearr.sort((a, b) => {
					if (b.count !== a.count) {
						return b.count - a.count;
					}
					return a.id - b.id;
				});
			},
			make_chart() {
				let res = {
					categories: this.pastDate,
					series: [{
						name: "缺勤人数",
						data: this.pastVal
					}]
				};
				this.chartData = res;
			},
		}
	};
</script>

<style>
	.charts-box {
		width: 750rpx;
		height: 500rpx;
		display: flex;
		justify-content: center;
		align-items: center;
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
</style>