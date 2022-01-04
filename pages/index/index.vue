<template>
	<view class="content">
		<view class="userInfo" @click="getUserInfo()">
			<image class="userInfo_userIcon" v-show="userIcon!=''" :src="userIcon" mode=""></image>
			<view class="userInfo_userName">
				{{userName}}
			</view>
		</view>
		<view class="clock">
			<view class="clock_status">
				<view class="clock_work">
					<view class="clock_work_top">
						{{date}} {{workOnMessage}}
					</view>
					<!-- <view class="clock_work_bottom">
						<image class="clock_work_img" v-show="workStatus>0" src="../../static/checked.jpg" mode="">
						</image>
						<view class="clock_work_message">
							{{workOnMessage}}
						</view>
					</view> -->
				</view>
				<!-- <view class="clock_work">
					<view class="clock_work_top">
						下班{{workDownTime}}
					</view>
					<view class="clock_work_bottom">
						<image class="clock_work_img" v-show="workStatus>1" src="../../static/checked.jpg" mode=""></image>
						<view class="clock_work_message">
							{{workDownMessage}}
						</view>
					</view>
				</view> -->
			</view>
			<view class="clock_clock" :class="{'clock_clock_on':workStatus==1}" type="default" @click="changeWorkStatus">
				<view class="clock_clock_title">
					{{clockTitle}}
				</view>
				<view class="clock_clock_time">
					{{nowTime}}
				</view>
			</view>
			<!-- 地理位置：latitude:{{location.latitude}},longitude:{{location.longitude}} -->
		</view>
	</view>
</template>

<script>
	export default {

		data() {
			return {
				workStatus: 0, // 0 未上班 1 已下班 
				workOnTime: "09:00",
				workDownTime: "18:00",
				workOnMessage: "",
				workDownMessage: "",
				nowTime: "",
				clockTitle: "",
				timeinterval: 0,
				userIcon: "/static/logo.png",
				userName: "张三",
				loginFlg: false,
				location: {
					latitude: 39,
					longitude: 116
				},
				date:""
			}
		},
		onLoad() {
			this.date = new Date().toLocaleDateString().replace(/\//g,"-")
			this.timeinterval = setInterval(() => {
				this.nowTime = new Date().toLocaleTimeString('chinese', {
					hour12: false
				})
			}, 1000)

		},
		methods: {
			changeWorkStatus() {

				this.getLocation()
				
			},
			getWorkStatus() {
				let _this = this
				return new Promise((resolve, reject) => {
					// api
					setTimeout(() => {
						if (_this.workStatus == 0) {
							resolve(1)
						}
					}, 1000)
				})

			},
			// 获取地理位置
			getLocation() {
				let _this = this
				uni.getLocation({
					type: "gcj02",
					success(res) {
						_this.location.latitude = res.latitude
						_this.location.longitude = res.longitude
						_this.getWorkStatus().then((res) => {
							_this.workStatus = res
						
						})
					},
					fail() {
						console.log("获取地理位置失败")
					}
				})


			},
			// 获取用户信息
			getUserInfo() {
				let _this = this
				uni.getUserProfile({
					desc: "需要获取头像及昵称",
					lang: "zh-CN",
					success(res) {
						_this.userIcon = res.userInfo.avatarUrl
						_this.userName = res.userInfo.nickName
					},
					fail(err) {
						console.log("获取用户信息失败", err)
					}
				})
			},
			// workstatus变化监听
			workStatusChanges(val, oldVal) {

				if (val == 0) {
					this.workOnMessage = "未打卡"
					this.clockTitle = "上班打卡"
				} else if (val == 1) {
					// this.workOnMessage = `${this.nowTime}已打卡`
					this.workOnMessage = "已打卡"
					this.clockTitle = "已打卡"
				} else {
					this.workDownMessage = `${this.nowTime}已打卡`
					this.clockTitle = "下班打卡"
				}


			}
		},
		watch: {
			workStatus: {
				handler: 'workStatusChanges',
				immediate: true
			},



		},
		computed: {
			// workOnMessage() {
			// 	if (this.workStatus == 0) {
			// 		return "未打卡"
			// 	} else {
			// 		return `${this.nowTime}已打卡`
			// 	}
			// },
			// workDownMessage() {
			// 	if (this.workStatus == 0 || this.workStatus == 1) {
			// 		return "未打卡"
			// 	} else if (this.workStatus == 2) {
			// 		return `${this.nowTime}已打卡`
			// 	}
			// },
			// clockTitle() {
			// 	if (this.workStatus == 0) {
			// 		return "上班打卡"
			// 	} else {
			// 		return "下班打卡"
			// 	}
			// }
		}
	}
</script>

<style lang="scss">
	.content {
		width: 100vm;
		height: 100vh;
		padding-top: 20rpx;
		background-color: #eeeeee;

		.userInfo {
			display: flex;
			width: 90vw;
			height: 140rpx;
			margin: auto;
			border-radius: 25rpx;
			align-items: center;
			background-color: #fff;

			.userInfo_userIcon {
				width: 41px;
				height: 41px;
				margin-left: 20rpx;
				border-radius: 5rpx;
			}

			.userInfo_userName {
				margin-left: 20rpx;
			}
		}

		.clock {
			width: 90vw;
			margin: 40rpx auto;
			padding: 40rpx 0;
			border-radius: 25rpx;
			background-color: #fff;

			.clock_status {
				display: flex;
				height: 100rpx;
				// padding: 40rpx 0;
				justify-content: space-around;

				.clock_work {
					display: flex;
					align-items: center;
					justify-content: center;
					flex-direction: column;
					width: 50vw;
					// box-shadow: 0px 0px 21px 0px #bbbbbb;
					border-radius: 20rpx;
					background-color: rgb(237,237,237);

					.clock_work_top {
						// margin-left: 30rpx;
					}

					.clock_work_bottom {
						display: flex;
						align-items: center;
						margin-left: 30rpx;
						color: rgb(117,117,117);
						.clock_work_img {
							width: 20rpx;
							height: 20rpx;
						}
					}
				}
			}

			.clock_clock {
				display: flex;
				margin: auto;
				margin-top: 50rpx;
				justify-content: center;
				align-items: center;
				flex-direction: column;
				width: 400rpx;
				height: 400rpx;
				background: linear-gradient(to bottom right,rgb(63,144,249), rgb(38,106,214));
				border-radius: 200rpx;
				box-shadow: 0px 10px 21px 0px #cee9ff;

				.clock_clock_title {
					font-size: 24px;
					// font-weight: bold;
					color: #fff;
				}

				.clock_clock_time {
					// font-weight: bold;
					color: rgba($color: #fff, $alpha: 0.5);
				}
			}
			.clock_clock_on{
				background: linear-gradient(to bottom right,rgb(185, 185, 185), rgb(148, 148, 148));
				box-shadow: 0px 10px 21px 0px #bebebe;
			}
		}
	}
</style>
