<template>
	<view>
		<view class="headContainer">
			<view class="dataBlocks" v-for="(item,index) in totalData">
				<view style="font-size: 50rpx; line-height: 130rpx;">{{item.text}}</view>
				<view style="font-size: 50rpx; line-height: 130rpx;">{{item.val}}</view>
			</view>
		</view>
		<view style="display: flex; justify-content: space-around;">
			<view style="margin: 20rpx 0;" @tap="checkedTaped" :class="{actived: showRed}">
				已打卡名单
			</view>
			<view style = "margin: 20rpx 0;" @tap="checkedTaped" :class="{actived: !showRed}">
				未打卡名单
			</view>
		</view>
		<view class="dataContainer" v-for="(item,index) in daka" v-if="showRed">
			<view>{{index}}</view>
			<view>{{item.name}}</view>
			<view>{{item.time}}</view>
		</view>
		<view class="dataContainer" v-for="(item,index) in notdaka"  v-if="!showRed">
			<view>{{index}}</view>
			<view>{{item.name}}</view>
			<view>{{item.id}}</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				showRed:1,
				totalData: [{
						text: '缺勤人数',
						val: '加载中...'
					},
					{
						text: '缺勤率',
						val: '加载中...'
					}
				],
				daka: [],
				notdaka: [],
			}
		},
		methods: {
			checkedTaped()
			{
				this.showRed = !this.showRed;
			},
			formatTime(date) {
				const year = date.getFullYear();
				const month = String(date.getMonth() + 1).padStart(2, '0');
				const day = String(date.getDate()).padStart(2, '0');
				const hours = String(date.getHours()).padStart(2, '0');
				const minutes = String(date.getMinutes()).padStart(2, '0');
				return `${year}-${month}-${day} ${hours}:${minutes}`;
			},
			async update_page() {
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
				console.log(data2);

				let classSize = uni.getStorageSync('classsize');
				let doneCount = 0;
				let now = new Date();
				let formatdate = this.formatTime(now).substr(0, 10);
				this.daka = [];
				let temparr = [];
				// console.log("record");
				for (let i = 0; i < data.length; i += 1) {
					let record = data[i];
					// console.log(record);
					if (record.date == formatdate) {
						this.daka.push({
							"name": record.name,
							"time": record.time
						});
						temparr.push({
							"name": record.name,
							"id": record.id
						});
						++doneCount;
					}
				}
				let allarr = [];
				for (let i = 0; i < data2.length; i += 1) {
					let person = data2[i];
					if (!person.admin)
						allarr.push({
							"name": person.name,
							"id": person.id
						});
				}
				this.totalData[0].val = classSize - doneCount;
				this.totalData[1].val = ((classSize - doneCount) / classSize * 100).toFixed(0) + '%';

				function isEqual(obj1, obj2) {
					return obj1.name == obj2.name && obj1.id == obj2.id;
				}

				function findMissingObjects(S, T) {
					const missingObjects = S.filter(objS => !T.some(objT => isEqual(objS, objT)));
					return missingObjects;
				}

				this.notdaka = findMissingObjects(allarr, temparr);
				// console.log("temparr");
				// console.log(temparr);
				// console.log("allarr");
				// console.log(allarr);
				// console.log("this.notdaka");
				// console.log(this.notdaka);
			},
		},
		onShow() {
			this.update_page();
		}
	}
</script>

<style>
	.actived{
		color: coral;
	}
	.headContainer {
		display: flex;
		justify-content: space-between;

		width: 742rpx;
		height: 300rpx;
		margin: 5rpx auto;
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