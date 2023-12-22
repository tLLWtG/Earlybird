<template>
	<view>
		<image class="backgroundImage" src="/static/shangxiang.gif"></image>
		<view class="titleContainer" style="font-size: 50rpx;">上电子香火，积赛博功德</view>
		<view class="luckContainer" :hidden="luckActive">
			<view class="lineContainer" style="line-height: 10vh;">【今日人品】{{jrrp}}</view>
			<view class="lineContainer" style="line-height: 10vh;">【宜】{{yi}}</view>
			<view class="lineContainer" style="line-height: 10vh;">【宜】{{yi}}</view>
			<view class="lineContainer" style="line-height: 10vh;">【忌】{{ji}}</view>
			<view class="lineContainer" style="line-height: 10vh;">【忌】{{ji}}</view>
			<button style="width: 400rpx height:30rpx;" @click="clickButtonReturn">返回</button>
		</view>
		<button class="buttons" @click="clickButtonQiuqian">上香</button>

	</view>
</template>

<script>
	function getDate() {
		let date = new Date;
		const year = date.getFullYear();
		const month = String(date.getMonth() + 1).padStart(2, '0');
		const day = String(date.getDate()).padStart(2, '0');
		return `${year}-${month}-${day}`;
	}

	function hashFunc(str, mod) {
		let hashCode = 0;
		for (var i = 0; i < str.length; i++) {
			hashCode = 233 * hashCode + str.charCodeAt(i);
			hashCode %= mod;
		}
		return hashCode;
	}
	export default {
		data() {
			return {
				screenHeight: '',
				luckActive: 1,
				qiuQianRecords: [],
				good: ["赶作业: 很快就能补完啦", "翘课: 不会被老师发现", "抽卡: 一发入魂", "放假: 自由自在的一个假期", "熬夜: 事情终究可以完成的", "查成绩: 春风得意马蹄疾，一日看尽长安花",
					"聊天: 能和人分享喜悦，露出最真实的一面吧", "刷题: 一遍过样例", "去食堂: 给了双倍的量", "体育锻炼: 身体棒棒哒", "写作文: 匆匆枯笔梦生花",
					"听课: 100% 消化", "卷: 感觉能做到的话，请务必坚持下来", "重构代码: 代码质量明显提高",
					"睡觉: 养足精力，明日再战", "看风景: 良辰美景，赏心乐事", "摸鱼: 不去着急的话，便又是一帆风顺的一天", "点外卖: 及时送到"
				],
				bad: ["写作文: 不知所云，离题万里", "直播写代码: CE, RE and T，身败名裂", "学数论: 咋看都不会", "吃泡面: 可能会没有调料包", "去食堂: 爱吃的菜刚被打完",
					"查成绩: 待到秋来九月八，我花开后百花杀", "翘课: 老师点名啦",
					"膜拜大佬: 被大佬鄙视", "考试: 一上考场就忘光了", "谈恋爱: 考试临头各自飞", "摸鱼: 浪费的时光总是要补回来的", "点外卖: 一直没有送到还不给退款", "装弱: 你太强了",
					"放假: 就放一天，全是作业", "体育锻炼: 消耗的能量全吃回来了", "熬夜: 爆肝，通宵干不完",
					"听课: 反正你听不懂", "看风景: 花开花落，再灿烂的星光也会消失"
				],
				showadmin: "",
				showid: "",
				showname: "",
				showclass: ""
			}
		},
		computed: {
			jrrp() {
				let hash = getDate();
				hash += this.showid;
				hash = hashFunc(hash, 101);
				return hash;
			},
			yi() {
				let hash = getDate();
				hash += this.showid * 2;
				hash = hashFunc(hash, this.good.length);
				return this.good[hash];
			},
			ji() {
				let hash = getDate();
				hash += this.showid * 3;
				hash = hashFunc(hash, this.bad.length);
				return this.bad[hash];
			}
		},
		onShow() {
			this.showid = uni.getStorageSync("showid");
			this.showname = uni.getStorageSync("showname");
			this.showclass = uni.getStorageSync("showclass");
			this.showadmin = uni.getStorageSync("showadmin");
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
			// 求签成功和已经求签的提示
			showToast_1() {
				uni.showToast({
					title: '你今天已求过签'
				})
			},
			showToast_2() {
				uni.showToast({
					title: '功德+1' //功德有空的话可以+一个随机数，增强趣味性
				})
			},
			// 点击求签之后的反应
			clickButtonQiuqian() {
				this.qiuQianRecords = uni.getStorageSync('qiuQianInfo') || [];
				this.luckActive = 0;
				var now = new Date();
				now = this.formatTime(now);
				//判断是否今天已经求过签
				if (this.qiuQianRecords.length > 0 && this.qiuQianRecords[0].substr(0, 10) == now.substr(0, 10)) {
					this.showToast_1();
				} else {
					this.showToast_2();
					this.qiuQianRecords.unshift(now);
					uni.setStorageSync('qiuQianInfo', this.qiuQianRecords);
				}
			},
			//弹出窗口里面的返回按键
			clickButtonReturn() {
				this.luckActive = 1;
			}
		},
		onLoad() {
			this.screenHeight = uni.getSystemInfoSync().windowHeight;
			console.log(1);
		}
	}
</script>

<style>
	.backgroundImage {
		width: 750rpx;
		height: 99vh;
		position: absolute;
		z-index: -1;
		opacity: 0.6;
	}

	.titleContainer {
		width: 750rpx;
		height: 200rpx;

		margin: 10rpx auto;

		line-height: 200rpx;
		text-align: center;
	}

	.buttons {
		position: absolute;

		bottom: 100rpx;
		left: 25%;

		width: 400rpx;
		height: 100rpx;
		background-color: cornsilk;
		
		line-height: 100rpx;
		font-size: 50rpx;
		text-align: center;
	}

	.luckContainer {
		margin: auto auto;
		width: 600rpx;
		height: 60vh;

		background-color: aliceblue;
		border-radius: 10rpx;

		display: flex;
		flex-direction: column;
		align-items: flex-start;
		justify-content: flex-start;
	}

	.lineContainer {
		width: 600rpx;

		margin-left: 30rpx;

		text-align: left;
	}
</style>