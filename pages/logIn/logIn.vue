<template>
	<view>
		<view class = "imageContainer">
			<image src = "../../static/user.png"></image>
		</view>
		<view class = "inputContianer">
			<view style="margin-bottom: 30rpx;">请输入用户名</view>
			<input class = "inputs" placeholder="用户名"/>
			<view style="margin-bottom: 30rpx;">请输入密码</view>
			<input class = "inputs" placeholder="密码"/>
		</view>
		<button class="logInButton">登录</button>
	</view>
</template>

<script>
</script>

<style lang="scss">
	.imageContainer{
		display: flex;
		justify-content: center;
		
		margin-top: 100rpx;
		margin-bottom: 0rpx;
		margin: 100rpx auto 50rpx auto;
		
		width:750rpx;
		height:300rpx;
	}
	.imageContainer image{
		height : 200rpx;
		width : 200rpx;
	}
	.inputContianer{
		display:flex;
		flex-direction: column;
		
		width:650rpx;
		margin:0 auto;
	}
	.inputContianer .inputs{
		width : 650rpx;
		height : 50rpx;
		margin-bottom: 20rpx;
	}
	.logInButton{
		width:400rpx;
		height:100rpx;
		margin:0 auto;
	}
</style>