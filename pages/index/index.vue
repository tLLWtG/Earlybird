<template>
	<view>
		<view class="title">每日打卡</view>
		<view class="subtitle">
			<view class="" style="text-align: center;">
				{{hitokoto}}
			</view>
			<view class="" style="text-align: center; margin-top: 20rpx;">
				from: {{hitokotoFrom}}
			</view>
		</view>
		<view v-if="!islogin">
			<view style="text-align: center;">请登录后再打卡</view>
		</view>
		<view v-if="islogin&&showadmin=='stu'" class="navigators">
			<navigator class="navigatorType" style="background-color: #ADD8E6;" url="/pages/daka/daka">打卡
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
			// async delete() {
			// 	const db = uniCloud.database();
			// 	db.collection('checkrecord').doc('658444ff9755e3cdbf9ebe84').remove();
			// }
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
		text-align: center;
	}

	.subtitle {
		width: 650rpx;
		height: 150rpx;

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