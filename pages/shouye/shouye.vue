<template>
	<view class="Grid">
		<!-- <view v-for="item in List" :class="setClass(item.electricity)" :key="item.Slot" @click="DJ" :id="item.Slot">
			<view class="GSimg"><image class="Image" :src="setImf(item.electricity)" mode="aspectFit"></image></view>
			<view class="GSdl">{{ item.electricity + "%"}}</view>
			<view class="GStitle">{{"充电宝" + item.Slot }}</view>
		</view> -->
		<view v-for="item in 12" :class="setClass(List.find(obj => obj.Slot === item))" :key="item" @tap="btnBClick" :id="item">
			<template v-if="List.find(obj => obj.Slot === item)">
				<view class="GSimg"><image id="img" class="Image" :src="setImf(List.find(obj => obj.Slot === item).electricity)" mode="aspectFit"></image></view>
				<view class="GSdl">{{ List.find(obj => obj.Slot === item).electricity + '%' }}</view>
				<view class="GStitle">{{ '充电宝' + List.find(obj => obj.Slot === item).Slot }}</view>
			</template>
			<template v-else>
				<view class="GSimg"><image class="Image" :src="setImf('空槽')" mode="aspectFit" id="img"></image></view>
				<view class="GSdl">--</view>
				<view class="GStitle">暂无</view>
			</template>
		</view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			List: [
				// {id:1,img:'../../static/chongdianbao.png',title:'充电宝1',dl:'71%'},
				// {id:2,img:'../../static/jubutianchongwuduandian-.png',title:'充电宝2',dl:'空槽'},
				// {id:3,img:'../../static/chongdianbao.png',title:'充电宝3',dl:'80%'},
				// {id:4,img:'../../static/jubutianchongwuduandian-.png',title:'充电宝4',dl:'空槽'},
				// {id:5,img:'../../static/chongdianbao.png',title:'充电宝5',dl:'55%'},
				// {id:6,img:'../../static/jubutianchongwuduandian-.png',title:'充电宝6',dl:'空槽'},
				// {id:7,img:'../../static/jubutianchongwuduandian-.png',title:'充电宝7',dl:'空槽'},
				// {id:8,img:'../../static/chongdianbao.png',title:'充电宝8',dl:'88%'},
				// {id:9,img:'../../static/chongdianbao.png',title:'充电宝9',dl:'23%'}
			]
		};
	},
	globalData() {
		id1 = ' ';
		userCode1 = ' ';
		Authorization1 = ' ';
	},
	onLoad: function(option) {
		console.log(option.password);
		getApp().globalData.id1 = option.id;
		getApp().globalData.userCode1 = option.userCode;
		getApp().globalData.Authorization1 = option.Authorization;
		console.log('密码=' + option.password);
		if (option.password == '88888888') {
			console.log('修改密码!');
			uni.reLaunch({
				url: '/pages/Upmima/Upmima?devSn=' + option.id + '&userCode=' + option.userCode + '&Authorization=' + option.Authorization
			});
		} else {
			console.log('刷新');
			this.getHomePosts1();
			this.getHomePosts();
		}
	},
	methods: {
		btnBClick(e) {
			// 此处用法为在js中调用，需要写uni.$u.throttle()
			if (this.List.findIndex(item => item.Slot == e.currentTarget.id) > -1) {
				this.$u.throttle(() => this.DJ(e), 10000);
			}
		},
		DJ(e) {
			var _self = this;
			uni.showLoading({ title: '借宝中', mask: true });
			console.log('getApp().globalData.id1=' + getApp().globalData.id1);
			uni.request({
				url: '/api/kaying/v1/huabang/borrow?devSn=' + getApp().globalData.id1 + '&slotNum=' + e.currentTarget.id + '&userCode=' + getApp().globalData.userCode1, //接口地址
				header: {
					Authorization: getApp().globalData.Authorization1
				},
				method: 'GET',
				success: res => {
					console.log(res);
					uni.hideLoading();
					if (res.data.result == 0) {
						// this.getHomePosts();
						uni.showToast({
							title: '充电宝租借成功',
							icon: 'none', //不要图标
							duration: 2000 //1后消失
						});

						_self.List = _self.List.filter(obj => obj.Slot != e.currentTarget.id);
						_self.$forceUpdate();
					} else {
						// this.getHomePosts();
						uni.showToast({
							title: res.data.msg,
							icon: 'none', //不要图标
							duration: 2000 //1后消失
						});
					}

					console.log('getHomePosts');

					// 因为第一次查询会有问题，所以查询两次，第一次查询的数据不展示
					setTimeout(() => {
						_self.getHomePosts(false);
					}, 0);
					setTimeout(() => {
						_self.getHomePosts();
					}, 1);
				}
			});
		},
		onPullDownRefresh() {
			// 因为第一次查询会有问题，所以查询两次，第一次查询的数据不展示
			setTimeout(() => {
				this.getHomePosts(false);
			}, 0);
			setTimeout(() => {
				this.getHomePosts();
				uni.stopPullDownRefresh();
			}, 3000);
		
			// setTimeout(() => {
			// 	this.getHomePosts();
			// 	uni.stopPullDownRefresh();
			// }, 2000);
		},
		setClass: function(a) {
			if (!a || a == '空槽') {
				return 'Grid-Item1';
			} else {
				return 'Grid-Item';
			}
		},
		setImf: function(a) {
			if (a == '空槽') {
				return '../../static/jubutianchongwuduandian-.png';
			} else {
				return '../../static/chongdianbao.png';
			}
		},
		getHomePosts(isShow = true) {
			var _self = this;
			console.log('getApp().globalData.id1=' + getApp().globalData.id1);
			uni.request({
				url: '/api/kaying/v1/huabang/select?devSn=' + getApp().globalData.id1, //接口地址
				header: {
					Authorization: getApp().globalData.Authorization1
				},
				method: 'GET',
				success: res => {
					if (!isShow) {
						return;
					}
					console.log('Authorization' + getApp().globalData.Authorization1);
					// 请求成功之后将文章列表数据赋值给List
					if (res.data.data.slotList == null) {
						uni.showToast({
							title: '充电宝已全部借出',
							icon: 'none', //不要图标
							duration: 3000 //1后消失
						});
						_self.List = null;
					} else {
						_self.List = res.data.data.slotList; //根据API数据找到对应的集合
						// uni.showModal({
						// 	cancelText: "取消", // 取消按钮的文字
						// 	confirmText: "确认", // 确认按钮文字
						// 	title: '提示',
						// 	content: '充电宝已全部借出',
						// 	confirmColor:'#3B8BFF',
						// 	cancelColor:'#222222',
						// 	success: res => {
						// 		if (res.confirm) {
						// 			console.log('ok')
						// 		} else if (res.cancel) {
						// 			// 取消
						// 			console.log('cancel')
						// 		}
						// 	}
						// });
					}
				},
				fail: err => {
					console.log(err);
				}
			});
		},
		getHomePosts1() {
			var _self = this;
			var leg = this.List;
			var shul = 9 - leg;
			console.log(leg);
			for (var i = 1; i <= shul; i++) {
				// res.data.data.slotList.chargingId = "";
				// res.data.data.slotList.Slot = leg+i;
				// res.data.data.slotList.slotState = "空槽";
				// res.data.data.slotList.electricity = 00
			}
		}
	}
};
</script>

<style>
.Grid {
	display: flex;
	flex-wrap: wrap;
	border-top: 1rpx solid #000000;
	justify-content: space-between;
	align-content: space-between;
	background: #f7f7f7;
	padding: 10rpx 0;
}
.GStitle {
	width: 100%;
	height: 10rpx;
	line-height: 34rpx;
	color: #06121e;
	font-size: 24rpx;
	margin-top: 15rpx;
}
.GSdl {
	width: 100%;
	height: 10rpx;
	line-height: 34rpx;
	color: #06121e;
	font-size: 24rpx;
	margin-top: 25rpx;
}
.Grid-Item {
	width: 30%;
	height: 213rpx;
	text-align: center;
	border: 1rpx solid #55ffff;
	box-sizing: border-box;
	background-color: aqua;
	margin: 30rpx 10rpx 10rpx 10rpx;
}
.Grid-Item1 {
	width: 30%;
	height: 213rpx;
	text-align: center;
	border: 1rpx solid #55ffff;
	box-sizing: border-box;
	background-color: #a6a6a6;
	margin: 30rpx 10rpx 10rpx 10rpx;
}
.GSimg {
	width: 120rpx;
	height: 120rpx;
	margin-left: 35rpx;
	margin-right: 10rpx;
	margin-bottom: 10rpx;
}
.Image {
	width: 150rpx;
	height: 150rpx;
}
</style>
