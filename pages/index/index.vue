<template>
	<view :class="className" class="full-bg">
		<!-- 组件显示 -->
		<view class="main-wrap" v-if="!(currentTab == 0 ? false : true)">
			<component_Monster class="full-bg" />
		</view>
		<view class="main-wrap" v-if="!(currentTab == 1 ? false : true)">
			<component_Masa class="full-bg" />
		</view>
		<view class="main-wrap" v-if="!(currentTab == 2 ? false : true)">
			<component_Ashin class="full-bg" :connected="connected" />
		</view>
		<view class="main-wrap" v-if="!(currentTab == 3 ? false : true)">
			<component_Stone class="full-bg" />
		</view>
		<view class="main-wrap" v-if="!(currentTab == 4 ? false : true)">
			<component_Ming class="full-bg" />
		</view>

		<!-- 自定义 tabbar -->
		<view class="nav-tabs">
			<view :class="'tab-list '+ (currentTab == idx ? 'active' : 'default')" :data-current="idx" @tap="swichNav"
				v-for="(item, idx) in items" :key="idx">
				<!-- <text class="tab-text" :data-current="idx" :src="currentTab == idx ? item.selectedIconPath : item.iconPath">{{ item.text }}</text> -->
				<image :class="'iconPath '+ (idx == currentTab ? 'bigimg' : '') " :data-current="idx"
					:data-class="item.className" :src="currentTab == idx ? item.selectedIconPath : item.iconPath">
				</image>
				<!-- <image :class="'iconPath ' + 'bounce' + idx + ' '+ (idx == 2 ? 'bigimg' : '') "  :data-current="idx" :src="currentTab == idx ? item.selectedIconPath : item.iconPath"></image> -->
			</view>
		</view>
	</view>
</template>

<script>
	const app = getApp();
	import component_Monster from '@/pages/component/Monster/index';
	import component_Masa from '@/pages/component/Masa/index';
	import component_Ashin from '@/pages/component/Ashin/index';
	import component_Stone from '@/pages/component/Stone/index';
	import component_Ming from '@/pages/component/Ming/index';

	export default {
		components: {
			component_Monster,
			component_Masa,
			component_Ashin,
			component_Stone,
			component_Ming
		},
		data() {
			return {
				connected: true,
				currentTab: 2,
				className: 'head-bg-as',
				items: [{
						iconPath: '/static/pages/img/bar-gs.png',
						selectedIconPath: '/static/pages/img/bar-gs.png',
						text: 'Monster',
						className: "head-bg-gs"
					},
					{
						iconPath: '/static/pages/img/bar-ms.png',
						selectedIconPath: '/static/pages/img/bar-ms.png',
						text: 'Masa',
						className: "head-bg-ms"
					},
					{
						iconPath: '/static/pages/img/bar-as.png',
						selectedIconPath: '/static/pages/img/bar-as.png',
						text: 'Ashin',
						className: "head-bg-as"
					},
					{
						iconPath: '/static/pages/img/bar-st.png',
						selectedIconPath: '/static/pages/img/bar-st.png',
						text: 'Stone',
						className: "head-bg-st"
					},
					{
						iconPath: '/static/pages/img/bar-gy.png',
						selectedIconPath: '/static/pages/img/bar-gy.png',
						text: 'Ming',
						className: "head-bg-gy"
					}
				]
			};
		},
		onLoad: function(options) {
			let deviceId = app.globalData.deviceId
			var that = this;
			console.log(options);
			uni.getBLEDeviceServices({
				deviceId: deviceId,
				success: function(res) {
					console.log(res.services);
					app.globalData.serviceId = res.services[0].uuid;
					uni.setStorageSync('serviceId', res.services[0].uuid);
					uni.getBLEDeviceCharacteristics({
						deviceId: deviceId,
						serviceId: res.services[0].uuid,
						success: function(res) {
							app.globalData.writeUuid = res.characteristics[1].uuid;
							uni.setStorageSync('writeUuid', res.characteristics[1].uuid);
							app.globalData.notifyUuid = res.characteristics[0].uuid;
							uni.setStorageSync('notifyUuid', res.characteristics[0].uuid);
							uni.notifyBLECharacteristicValueChange({
								state: true,
								deviceId: deviceId,
								serviceId: res.services[0].uuid,
								characteristicId: res.characteristics[0].uuid,
								success: function(res) {
									console.log('启用notify成功：' + res
										.characteristics[0].uuid);
									console.log(JSON.stringify(res));
								},
								fail: function() {
									console.log('开启notify失败' + res
										.characteristics);
								}
							});
						}
					});
				}
			});
			uni.onBLEConnectionStateChange(function(res) {
				console.log(res.connected);
				that.setData({
					connected: res.connected
				});
			});
		},
		methods: {
			swichNav: function(e) {
				console.log(e);
				let that = this;
				if (this.currentTab === e.target.dataset.current) {
					return false;
				} else {
					that.setData({
						currentTab: e.target.dataset.current,
						className: e.target.dataset.class
					});
				}
			}
		}
	};
</script>
<style lang="scss" scoped>
	.main-wrap {
		padding-top: 50px;
		height: calc(100% - 62px);
	}

	.full-bg {
		height: 100%;
	}

	@keyframes bounce0 {

		0%,
		20%,
		50%,
		80%,
		100% {
			transform: translateY(10px);
		}

		20% {
			transform: translateY(-10px);
		}

		40% {
			transform: translateY(-5px);
		}
	}

	@keyframes bounce1 {

		0%,
		20%,
		50%,
		80%,
		100% {
			transform: translateY(0px);
		}

		20% {
			transform: translateY(-10px);
		}

		40% {
			transform: translateY(-5px);
		}
	}

	@keyframes bounce3 {

		0%,
		20%,
		50%,
		80%,
		100% {
			transform: translateY(-5px);
		}

		20% {
			transform: translateY(-10px);
		}

		40% {
			transform: translateY(-5px);
		}
	}

	@keyframes bounce4 {

		0%,
		20%,
		50%,
		80%,
		100% {
			transform: translateY(5px);
		}

		20% {
			transform: translateY(-10px);
		}

		40% {
			transform: translateY(-5px);
		}
	}

	/**index.wxss**/
	.head-bg-gs {
		background-image: url('https://6d61-maydaysync-2gaijzhh7553fabf-1327815928.tcb.qcloud.la/maydayimgs/images/bg/monster.jpg?sign=038fccdf32268b6c5b7c551c1da985ca&t=1720706816');
		background-size: 100% 100%;
	}

	.head-bg-ms {
		background-image: url('https://6d61-maydaysync-2gaijzhh7553fabf-1327815928.tcb.qcloud.la/maydayimgs/images/bg/masa.jpg?sign=ddc79f64ab1aaa0b0f72d6df8a862a9f&t=1720706679');
		background-size: 100% 100%;
	}

	.head-bg-as {
		background-image: url('https://6d61-maydaysync-2gaijzhh7553fabf-1327815928.tcb.qcloud.la/maydayimgs/images/bg/ashin.jpg?sign=2928ff9272259a84b96506fe0716a394&t=1720705784');
		background-size: 100% 100%;
	}

	.head-bg-st {
		background-image: url('https://6d61-maydaysync-2gaijzhh7553fabf-1327815928.tcb.qcloud.la/maydayimgs/images/bg/stone.jpg?sign=69a7c733feb217f94b90a3a6462a4639&t=1720705799');
		background-size: 100% 100%;
	}

	.head-bg-gy {
		background-image: url('https://6d61-maydaysync-2gaijzhh7553fabf-1327815928.tcb.qcloud.la/maydayimgs/images/bg/ming.jpg?sign=9b22b6362a67c1fe1772920f02aa0571&t=1720705760');
		background-size: 100% 100%;
	}

	page {
		display: flex;
		flex-direction: column;
		height: 100%;
	}

	.nav-tabs {
		width: 100%;
		display: flex;
		position: fixed;
		bottom: 0;
	}

	.tab-list {
		display: flex;
		align-items: center;
		justify-content: center;
		flex-direction: column-reverse;
		// background: #fcfcfc;
		height: 120rpx;

	}

	.tab-text {
		font-size: 24rpx;
		line-height: 35rpx;
		color: #5f5f5f;
	}

	.bigimg {
		width: 74rpx !important;
		height: 74rpx !important;
		position: absolute;
		top: 0.5rem;
	}

	.iconPath {
		width: 54rpx;
		height: 54rpx;

		&.bounce0 {
			animation: bounce0 1s infinite;
		}

		&.bounce1 {
			animation: bounce1 1s infinite;
		}

		&.bounce3 {
			animation: bounce3 1s infinite;
		}

		&.bounce4 {
			animation: bounce4 1s infinite;
		}
	}

	.tab-content {
		flex: 1;
	}

	.default {
		line-height: 75rpx;
		text-align: center;
		flex: 1;
		/* border-bottom: 1px solid #eee; */
		color: #eee;
		font-weight: bold;
		font-size: 28rpx;
	}

	.active {
		line-height: 75rpx;
		text-align: center;
		color: black;
		flex: 1;
		/* border-bottom: 1px solid red; */
		font-weight: bold;
		font-size: 28rpx;
	}

	.show {
		display: block;
		flex: 1;
	}

	.hidden {
		display: none;
		flex: 1;
	}
</style>