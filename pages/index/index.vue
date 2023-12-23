<template>
	<view style=" height: 100vh;">
		<view class="subtitle">
			<view class="" style="text-align: center;">
				{{hitokoto}}
			</view>
			<view class="" style="text-align: center; margin-top: 20rpx;">
				----{{hitokotoFrom}}
			</view>
		</view>

		<view v-if="!islogin">
			<view style="text-align: center; font-size: 50rpx; margin-top: 300rpx;">请登录后再打卡</view>
		</view>

		<view v-if="islogin&&showadmin=='stu'" class="navigators" style="margin-top: 100rpx;">
			<view class="navigatorBlock">
				<view style="background-image: url(../../static/clipboard-check.svg);" class="imageType">
					<navigator url="/pages/student/daka/daka" class="navigatorType"></navigator>
				</view>
				<view style="line-height: 30rpx; text-align: center; font-style: Chewy; font-size: 40rpx;">打卡</view>
			</view>
			<view class="navigatorBlock" v-for="(item,index) in StudentNavigatorItems">
				<view class="imageType" :style="{ 'background-image': 'url(' + item.src + ')' }">
					<navigator :url='item.url' class="navigatorType"></navigator>
				</view>
				<view style="line-height: 30rpx; text-align: center; font-size: 40rpx;">{{item.text}}</view>
			</view>
		</view>

		<view v-if="islogin&&showadmin=='admin'" class="navigators" style="margin-top: 100rpx;">
			<view class="navigatorBlock" v-for="(item,index) in administorNavigatorItems">
				<view class="imageType" :style="{ 'background-image': 'url(' + item.src + ')' }">
					<navigator :url='item.url' class="navigatorType"></navigator>
				</view>
				<view style="line-height: 30rpx; text-align: center; font-size: 40rpx;">{{item.text}}</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				StudentNavigatorItems: [{
						src: '../../static/dice-5.svg',
						text: "求签",
						url: '/pages/student/qiuqian/qiuqian'
					},
					{
						src: '../../static/people.svg',
						text: '查看打卡排名',
						url: '/pages/student/totalData/totalData'
					},
					{
						src: "../../static/person.svg",
						text: "查看个人数据",
						url: '/pages/student/selfData/selfData'
					},
				],
				administorNavigatorItems: [{
						src: "../../static/bar-chart.svg",
						text: '今日打卡数据',
						url: '/pages/administor/dayData'
					},
					{
						src: "../../static/graph-up.svg",
						text: '历史数据汇总',
						url: '/pages/administor/historyData'
					}
				],
				islogin: false,
				showadmin: "",
				showid: "",
				showname: "",
				showclass: "",
				hitokoto: "hitokoto :D 获取中...",
				hitokotoFrom: ""
			}
		},
		onShow() {
			// this.delete()
			wx.request({
				url: 'https://v1.hitokoto.cn?c=a&c=d&c=i&c=k',
				method: 'GET',
				success: (res) => {
					console.log(res.data);
					this.hitokoto = res.data.hitokoto;
					this.hitokotoFrom = res.data.from;
				}
			})
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
			async delete() {
				const db = uniCloud.database();
				db.collection('checkrecord').doc('65850bac213929458dd8c7da').remove();
			}
		}
	}
</script>

<style>
	.subtitle {
		width: 650rpx;
		height: 200rpx;

		margin: 0 auto;

		line-height: 200rpx;
		font-size: 150rpx;
		text-align: center;
	}

	.subtitle {
		width: 650rpx;
		height: 150rpx;


		margin: 50rpx auto;

		line-height: 80rpx;
		font-size: 40rpx;
		text-align: center;

		radius: 10rpx;
	}

	.navigators {
		display: flex;
		justify-content: space-around;

		width: 750rpx;

		flex-wrap: wrap;
	}

	.navigators .navigatorBlock {
		display: flex;
		flex-direction: column;
		margin: 0 30rpx
	}

	.navigators .navigatorBlock .imageType {
		display: flex;
		justify-content: center;
		align-items: center;

		width: 200rpx;
		height: 200rpx;

		margin: 30rpx auto;

		line-height: 150rpx;
		text-align: center;

		background-position: center;
		background-size: cover;
	}

	.navigators .navigatorBlock .imageType .navigatorType {
		width: 190rpx;
		height: 190rpx;

		margin: 30rpx auto;

		line-height: 150rpx;
		text-align: center;

		background-position: center;
		background-size: cover;
	}
</style>