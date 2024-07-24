<template>
	<view class="content head-bg">
		<view class="text-area">
			<uni-section :title="item.name" type="line" v-for="item in List" :key="item.name">
				<uni-group mode="card">
					<swiper :indicator-dots="true" :style="{height: getSwiperHeight(item.data)}"  v-if="item.swiper">
						<swiper-item v-for="(valueItem,index) in item.data" :key="index">
							<u-grid :border="true" col="3">
								<u-grid-item v-for="(listItem,listIndex) in valueItem.data" :key="listIndex"
									@click="submit('aaaa',listItem)">
									<image class="fui-avatar__img" :src="listItem"></image>
								</u-grid-item>
							</u-grid>
							<u-toast ref="uToast" />
						</swiper-item>
					</swiper>
					
					<u-grid :border="true" col="3" v-if="!item.swiper">
						<u-grid-item v-for="(listItem,listIndex) in item.data" :key="listIndex"
							@click="submit('aaaa',listItem)">
							<image class="fui-avatar__img" :src="listItem"></image>
						</u-grid-item>
					</u-grid>
					<u-toast ref="uToast" v-if="!item.swiper" />
				</uni-group>
			</uni-section>
		</view>
		<TabBar class="bom-tabbar" :pagePath="'/pages/Ashin/index'"></TabBar>
	</view>
</template>

<script>
	const app = getApp();
	import {
		showImages
	} from "@/utils/ashin.js";
	export default {
		options: {
			styleIsolation: 'shared'
		},
		props: {
			connected: {
				type: Boolean,
				default: true
			}
		},
		data() {
			return {
				services: {},
				connectedDeviceId: '',
				List: showImages,
				wmls:[
					'https://6d61-maydaysync-2gaijzhh7553fabf-1327815928.tcb.qcloud.la/maydayimgs/images/Ashin/wmls/1.jpg?sign=585032558758e71f002bdfe71273d29d&t=1721829577',
					'https://6d61-maydaysync-2gaijzhh7553fabf-1327815928.tcb.qcloud.la/maydayimgs/images/Ashin/wmls/2.jpg?sign=c18b7c83cd3758958d25c70de8521922&t=1721829608',
					'https://6d61-maydaysync-2gaijzhh7553fabf-1327815928.tcb.qcloud.la/maydayimgs/images/Ashin/wmls/3.jpg?sign=f9223ea8654ea2d7d1ff3db491710975&t=1721829623',
					'https://6d61-maydaysync-2gaijzhh7553fabf-1327815928.tcb.qcloud.la/maydayimgs/images/Ashin/wmls/4.jpg?sign=bd26602e9274489bec8727907dcc8903&t=1721829633'
				]
			}
		},
		mounted() {
			console.log(showImages);
		},
		methods: {
			submit(name, kind) {
				console.log(name, kind);
				this.Send(name, kind)
			},
			Send(name, kind) {
				var that = this;
				let deviceId = app.globalData.deviceId
				let serviceId = app.globalData.serviceId
				let writeUuid = app.globalData.writeUuid
				if (that.connected) {
					var buffer = new ArrayBuffer(kind.length);
					var dataView = new Uint8Array(buffer);
					for (var i = 0; i < kind.length; i++) {
						dataView[i] = kind.charCodeAt(i);
					}
					uni.writeBLECharacteristicValue({
						deviceId: deviceId,
						serviceId: serviceId,
						characteristicId: writeUuid,
						value: buffer,
						success: function(res) {
							console.log('发送指令成功:' + res.errMsg);
						},
						fail: function(res) {
							console.log('message发送失败:' + res.errMsg);
							uni.showToast({
								title: '数据发送失败，请稍后重试',
								icon: 'none'
							});
						}
					});
				} else {
					uni.showModal({
						title: '提示',
						content: '蓝牙已断开',
						showCancel: false,
						success: function(res) {
							that.setData({
								searching: false
							});
						}
					});
				}
			},
			getSwiperHeight(itemCount) {
				console.log(itemCount);
				let maxDataItem = null;
				let maxDataCount = 0;
			
				itemCount.forEach(item => {
					if (item.data.length > maxDataCount) {
						maxDataItem = item;
						maxDataCount = item.data.length;
					}
				});
					
				const baseHeight = 150; // base height in rpx
				const additionalHeight = 150; // height per item in rpx
				const rows = Math.ceil(maxDataCount / 3); // 3 items per row
				console.log(itemCount,rows);
				return `${baseHeight + (rows * additionalHeight)}rpx`;
			}
		},
		watch: {
			connected(val) {
				console.log(val);
			}
		}
	}
</script>

<style>
	.swiper {
		min-height: 150rpx;
	}

	.group--uni-group {
		background-color: rgba(255, 255, 255, 0.9);
	}

	.uni-section__content-title {
		font-size: 16px !important;
		color: black !important;
	}

	.uni-section-header {
		padding: 0 10px !important;
	}

	.section--uni-section {
		background-color: transparent;
	}

	.u-grid-item {
		padding: 10px;
	}

	.fui-avatar__img {
		width: 72px;
		height: 72px;
		border-radius: 50%;
	}

	.uni-col {
		margin-bottom: 10px;
	}

	.card--uni-card {
		background-color: rgba(255, 255, 255, 0.5);
	}

	.content {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
	}

	.logo {
		height: 200rpx;
		width: 200rpx;
		margin-top: 200rpx;
		margin-left: auto;
		margin-right: auto;
		margin-bottom: 50rpx;
	}

	.text-area {
		width: 100%;
		height: calc(100% - 120px);
		overflow-x: auto;
		position: absolute;
	}

	.title {
		font-size: 36rpx;
		color: #8f8f94;
	}

	.head-bg {
		height: 100%;
	}
</style>
