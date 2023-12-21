<template>
	<view>
		<view class="title">每日打卡</view>
		<view class="subtitle">开卷开卷开卷开卷开卷开卷开卷开卷开卷开卷</view>
		<view v-if="!islogin">
			<view style="text-align: center;">请登录后再打卡</view>
		</view>
		<view v-if="islogin&&showadmin=='stu'" class="navigators">
			<navigator class="navigatorType" style="background-color: #ADD8E6;" url="/pages/daka/daka" @tap="daka">打卡
			</navigator>
			<navigator class="navigatorType" style="background-color: #98FB98;" url="/pages/qiuqian/qiuqian">求签
			</navigator>
			<navigator class="navigatorType" v-for="(item,index) in navigatorItems"
				:style="{backgroundColor:item.color}" :url='item.url'>
				{{item.text}}
			</navigator>
		</view>
		<view v-if="islogin&&showadmin=='admin'">
			<view style="text-align: center;">管理员页面</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				navigatorItems: [{
						color: '#FFD700',
						text: '查看打卡排名',
						url: '/pages/totalData/totalData'
					},
					{
						color: "#FFC0CB",
						text: "查看个人数据",
						url: '/pages/selfData/selfData'
					}
				],
				islogin: false,
				showadmin: "",
				showid: "",
				showname: "",
				showclass: ""
			}
		},
		onShow() {
			let loginState = uni.getStorageSync("loginState");
			if (loginState != "") {
				this.islogin = true;
				this.showid = uni.getStorageSync("showid");
				this.showname = uni.getStorageSync("showname");
				this.showclass = uni.getStorageSync("showclass");
				this.showadmin = uni.getStorageSync("showadmin");
			} else
				this.islogin = false;
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
				}
				//将打卡数据传回
				uni.setStorageSync('dakaInfo', dakaRecord);
			}

		}
	}
</script>

<style>
	.title {
		width: 650rpx;
		height: 200rpx;

		margin: 0 auto;

		line-height: 200rpx;
		font-size: 150rpx;
		text-align: left;
	}

	.subtitle {
		width: 650rpx;
		height: 50rpx;

		margin: 50rpx auto;

		line-height: 50rpx;
		font-size: 30rpx;
		text-align: left;
	}

	.navigators {
		display: flex;
		justify-content: space-around;

		width: 750rpx;
		height: 100rpx;

		flex-wrap: wrap;
	}

	.navigatorType {
		width: 300rpx;
		height: 150rpx;

		margin: 30rpx auto;

		line-height: 150rpx;
		text-align: center;
	}
</style>