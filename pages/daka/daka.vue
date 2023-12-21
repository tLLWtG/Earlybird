<template>
	<view>
		<view class="sentenceContainer">别卷了，快点回去睡觉</view>
		<view class="dakaSuccessContainer">
			<view>你已经在今日{{hour}}打卡成功</view>
		</view>
		<view class="dakaSuccessContainer">
			<view>你是今天第{{rank}}个打卡的人</view>
		</view>
		<view class="dakaSuccessContainer">
			<view>你已经成功击败了{{percent}}%的人</view>
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
				rank: 1,
				percent: 100,
			}
		},
		methods: {

		},
		onLoad() {
			this.dakaRecords = uni.getStorageSync('dakaInfo');
			this.hour = this.dakaRecords[0].substr(10, 12);
			console.log(this.dakaRecords[0].substr(0, 10));
		}
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