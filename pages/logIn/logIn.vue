<template>
	<view>
		<view class="imageContainer">
			<image src="../../static/user.png"></image>
		</view>

		<view v-if="islogin" class="cont">
			<view v-if="showadmin=='stu'" class="cont">
				<view class="cont" style="text-align: center;">
					姓名：{{showname}}
				</view>
				<view class="cont" style="text-align: center;">
					学号：{{showid}}
				</view>
				<view class="cont" style="text-align: center;">
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
				<view class="cont" style="text-align: center;">
					管理班级：{{showclass}}
				</view>
			</view>
		</view>
		<view class="inputContianer">
			<view style="margin-bottom: 30rpx;">请输入账号</view>
			<input class="inputs" @input="onKeyInput1" placeholder="学号/工号" />
			<view style="margin-bottom: 30rpx;">请输入密码</view>
			<input class="inputs" @input="onKeyInput2" placeholder="密码" />
		</view>
		<button class="logInButton" @click="checkLogin">登录</button>
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
				this.showid = uni.getStorageSync("showid");
				this.showname = uni.getStorageSync("showname");
				this.showadmin = uni.getStorageSync("showadmin");
				this.showclass = uni.getStorageSync("showclass");
			}
			else
				this.islogin = false;
		},
		methods: {
			onKeyInput1: function(event) {
				this.id = event.target.value;
			},
			onKeyInput2: function(event) {
				this.password = event.target.value;
			},
			async checkLogin() {
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
				uni.setStorageSync("classsize", data.length - 1);
				for (let i = 0; i < data.length; i += 1) {
					let person = data[i];
					// console.log(person);
					// console.log(person.id);
					// console.log(person.password);
					if (person.id == this.id && person.password == this.password) {
						if (person.admin) {
							uni.showToast({
								title: '登陆成功'
							});
							uni.setStorageSync("loginState", "admin");
							this.showadmin = "admin";
						} else {
							uni.showToast({
								title: '登陆成功'
							});
							uni.setStorageSync("loginState", "stu");
							this.showadmin = "stu";
						}
						this.islogin = true;
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
					title: '账号或密码错误'
				});
				this.islogin = false;
				uni.setStorageSync("loginState", "");
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
	}

	.logInButton {
		width: 400rpx;
		height: 100rpx;
		margin: 0 auto;
	}
</style>