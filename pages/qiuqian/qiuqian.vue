<template>
	<view>
		 <image class = "backgroundImage" src="../../static/shangxiang.gif"></image>
		 <view class = "titleContainer">上电子香火，积赛博功德</view>
		 <view class = "luckContainer"  :hidden = "luckActive">
			 <view class = "lineContainer" style="line-height: 10vh;">今日运势:</view>
			 <view class = "lineContainer" style="line-height: 5vh;">大吉</view>
			 <view class = "lineContainer" style="line-height: 10vh;">今日，宜:</view>
			 <view class = "lineContainer" style="line-height: 5vh;">开摆</view>
			 <view class = "lineContainer" style="line-height: 10vh;">今日，忌:</view>
			 <view class = "lineContainer" style="line-height: 5vh;">内卷</view>
			 <button style="width: 400rpx height:30rpx; background-color: aqua;" @click="clickButtonReturn">返回</button>
		 </view>
		 <button class = "buttons" @click="clickButtonQiuqian">上香</button>
		 
	</view>
</template>

<script>
	export default {
			data() {
				return {
					screenHeight:'',
					luckActive:1,
					qiuQianRecords:[]
				}
			},
			methods: {
				formatTime(date)
				{
					  const year = date.getFullYear();
					  const month = String(date.getMonth() + 1).padStart(2, '0');
					  const day = String(date.getDate()).padStart(2, '0');
					  const hours = String(date.getHours()).padStart(2, '0');
					  const minutes = String(date.getMinutes()).padStart(2, '0');
					  return `${year}-${month}-${day} ${hours}:${minutes}`;
				},
				// 求签成功和已经求签的提示
				showToast_1()
				{
					uni.showToast({
						title:'你今天已求过签'
					})
				},
				showToast_2()
				{
					uni.showToast({
						title:'功德+1'//功德有空的话可以+一个随机数，增强趣味性
					})
				},
				// 点击求签之后的反应
				clickButtonQiuqian()
				{
					this.qiuQianRecords = uni.getStorageSync('qiuQianInfo')||[];
					this.luckActive = 0;	
					var now = new Date();
					now = this.formatTime(now);
					//判断是否今天已经求过签
					if(this.qiuQianRecords.length > 0 && this.qiuQianRecords[0].substr(0,10) == now.substr(0,10))
					{
						this.showToast_1();
					}
					else
					{
						this.showToast_2();
						this.qiuQianRecords.unshift(now);
						uni.setStorageSync('qiuQianInfo', this.qiuQianRecords);
					}
				},
				//弹出窗口里面的返回按键
				clickButtonReturn()
				{
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
	.backgroundImage{
		width:750rpx;
		height:99vh;
		position:absolute;
		z-index: -1;
		opacity: 0.6;
	}
	.titleContainer{
		width: 750rpx;
		height: 200rpx;
		
		margin:10rpx auto;
		
		line-height: 200rpx;
		text-align: center;
	}
	.buttons{
		position: absolute;
		
		bottom: 100rpx;
		left:25%;
		
		width:400rpx;
		height: 100rpx;
	}
	.luckContainer{
		margin:auto auto;
		width:600rpx;
		height:60vh;
		
		background-color: aliceblue;
		border-radius: 10rpx;
		
		display: flex;
		flex-direction: column;
		align-items: flex-start;
		justify-content: flex-start;
	}
	.lineContainer{
		width:600rpx;
		
		margin-left: 30rpx;
		
		text-align: left;	
	}
	
</style>