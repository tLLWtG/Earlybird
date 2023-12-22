<template>
	<view>
		<view class="imageContainer">
			<image src="../../static/person.svg"></image>
		</view>

		<view v-if="islogin" class="cont">
			<view v-if="showadmin=='stu'" class="cont">
				<view class="cont" style="text-align: center;">
					姓名：{{showname}}
				</view>
				<view class="cont" style="text-align: center;">
					学号：{{showid}}
				</view>
				<view class="cont" style="text-align: center; margin-bottom: 30rpx;">
					班级：{{showclass}}
				</view>
			</view>
			<view v-if="showadmin=='admin'" class="cont">
				<view class="cont" style="text-align: center;">
					姓名：{{showname}}
				</view>
				<view class="cont" style="text-align: center;">
					工号：{{showid}}
				</view>
				<view class="cont" style="text-align: center; margin-bottom: 30rpx;">
					管理班级：{{showclass}}
				</view>
			</view>
			<button class="logInButton" @click="checkLogout">登出</button>
		</view>
		<view v-if = "!islogin" class ="inputContianer">
			<view style="margin-bottom: 30rpx;">请输入学号</view>
			<input class="inputs" @input="onKeyInput1" />
			<view style="margin-bottom: 30rpx;">请输入密码</view>
			<input class="inputs" @input="onKeyInput2" type="password" />
			<button class="logInButton" @click="checkLogin" v-if = "!islogin">登录</button>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				id: "",
				password: "",
				showid: "",
				showname: "",
				showadmin: "",
				showclass: "",
				islogin: false
			}
		},
		onShow() {
			let loginState = uni.getStorageSync("loginState");
			if (loginState != "")
			{
				this.islogin = true;
				this.showadmin = uni.getStorageSync('showadmin');
				this.showname = uni.getStorageSync('showname');
				this.showid = uni.getStorageSync('showid');
				this.showclass = uni.getStorageSync('showclass');
			}	
			else
				this.islogin = false;
			console.log(this.showadmin);
		},
		onLoad() {
			
		},
		methods: {
			onKeyInput1: function(event) {
				this.id = event.target.value;
			},
			onKeyInput2: function(event) {
				this.password = event.target.value;
			},
			checkLogout()
			{
				this.islogin = false;
				this.showadmin = '';
				this.showname ='';
				this.showid = '';
				this.showclass = ''
			},
			async checkLogin() {
				uni.showLoading({
					title:'登录中'
				});
				const db = uniCloud.database();
				let res = await db.collection('login').get();
				// console.log(res);
				// console.log(res.success);
				// console.log(res.result.data);
				// console.log(this.id);
				// console.log(this.password);
				let isSuccess = res.success;
				let data = res.result.data;
				if (!isSuccess) {
					uni.showToast({
						icon: 'error',
						title: '网络错误！'
					});
					return;
				}
				for (let i = 0; i < data.length; i += 1) {
					let person = data[i];
					// console.log(person);
					// console.log(person.id);
					// console.log(person.password);
					if (person.id == this.id && person.password == this.password) {
						if (person.admin) {
							uni.showToast({
								title: '登录成功' 
							});
							uni.setStorageSync("loginState", "admin");
							this.showadmin = "admin";
						} else {
							uni.showToast({
								title: '登录成功'
							});
							uni.setStorageSync("loginState", "stu");
							this.showadmin = "stu";
						}
						this.islogin = true;
						uni.hideLoading();
						this.showclass = person.class;
						this.showid = person.id;
						this.showname = person.name;
						uni.setStorageSync("showclass", this.showclass);
						uni.setStorageSync("showid", this.showid);
						uni.setStorageSync("showname", this.showname);
						uni.setStorageSync("showadmin", this.showadmin);
						return;
					}
				}
				uni.showToast({
					icon: 'error',
					title: '学号或密码错误'
				});
				this.islogin = false;
				uni.setStorageSync("loginState", "");
				uni.hideLoading()
			}
		}
	}
</script>

<style lang="scss">
	.cont {
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-content: center;
		
		font-size: 50rpx;
		line-height: 80rpx;
	}

	.imageContainer {
		display: flex;
		justify-content: center;

		margin-top: 100rpx;
		margin-bottom: 0rpx;
		margin: 100rpx auto 50rpx auto;

		width: 750rpx;
		height: 300rpx;
	}

	.imageContainer image {
		height: 200rpx;
		width: 200rpx;
	}

	.inputContianer {
		display: flex;
		flex-direction: column;

		width: 650rpx;
		margin: 0 auto;
	}

	.inputContianer .inputs {
		width: 650rpx;
		height: 50rpx;
		margin-bottom: 20rpx;
		
		border:2rpx solid #D3D3D3;
		border-radius: 10rpx;
	}

	.logInButton {
		width: 650rpx;
		height: 100rpx;
		margin: 0 auto;
		
		background-color: #F5F5DC;
		font-size: 50rpx;
		text-align: center;
		line-height: 100rpx;
	} 
	
</style>