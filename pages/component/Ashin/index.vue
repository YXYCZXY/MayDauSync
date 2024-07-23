<template>
	<view class="content head-bg">
		<view class="text-area">
			<uni-section :title="item.name" type="line" v-for="item in List" :key="item.name">
				<uni-group mode="card">
					<swiper :indicator-dots="true" class="swiper" v-if="item.swiper">
						<swiper-item v-for="(valueItem,index) in item.data" :key="index">
							<u-grid :border="true" col="3">
								<!-- 注意wmls这个数组一定要是偶数 偶数的话效果会好看些 -->
								<u-grid-item v-for="(listItem,listIndex) in valueItem" :key="listIndex"
									@click="submit('aaaa',listItem)">
									<image class="fui-avatar__img" :src="listItem"></image>
									<!-- <sync-card title="胡胡胡罗"  :src="listItem"></sync-card> -->
								</u-grid-item>
							</u-grid>
							<u-toast ref="uToast" />
						</swiper-item>
					</swiper>
					<u-grid :border="true" col="3" v-if="!item.swiper">
						<!-- 注意wmls这个数组一定要是偶数 偶数的话效果会好看些 -->
						<u-grid-item v-for="(listItem,listIndex) in item.data" :key="listIndex"
							@click="submit('aaaa',listItem)">
							<image class="fui-avatar__img" :src="listItem"></image>
							<!-- <sync-card title="胡胡胡罗"  :src="listItem"></sync-card> -->
						</u-grid-item>
					</u-grid>
					<u-toast ref="uToast" v-if="!item.swiper" />
					<!-- 							<uni-row gutter="20" class="demo-uni-row" :width="nvueWidth">
								<uni-col :span="8" v-for="url in wmls" :key="url">
									<sync-card title="胡胡胡罗"  :src="url"></sync-card>
								</uni-col>
							</uni-row> -->
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
				wmls: [
					'https://6d61-maydaysync-2gaijzhh7553fabf-1327815928.tcb.qcloud.la/maydayimgs/images/Ashin/wmls/wmls4.png?sign=84e2ea5a6171caa96c4104947f385f8f&t=1720972858',
					'https://6d61-maydaysync-2gaijzhh7553fabf-1327815928.tcb.qcloud.la/maydayimgs/images/Ashin/wmls/wmls3.png?sign=3d4f10d6f8c3697f551cc844cd7c09c4&t=1720972870',
					'https://6d61-maydaysync-2gaijzhh7553fabf-1327815928.tcb.qcloud.la/maydayimgs/images/Ashin/wmls/wmls2.png?sign=028ad74ca2fc7ead6c05423e1f007d7f&t=1720972881',
					'https://6d61-maydaysync-2gaijzhh7553fabf-1327815928.tcb.qcloud.la/maydayimgs/images/Ashin/wmls/wmls1.png?sign=b56ca9bf8420dabfba052e2a475bf6b0&t=1720972888',
					'https://6d61-maydaysync-2gaijzhh7553fabf-1327815928.tcb.qcloud.la/maydayimgs/images/Ashin/wmls/wmls1.png?sign=b56ca9bf8420dabfba052e2a475bf6b0&t=1720972888',
					''
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
							// fail
							//console.log(that.data.services)
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
		/* top: 200rpx; */
	}

	.title {
		font-size: 36rpx;
		color: #8f8f94;
	}

	.head-bg {
		height: 100%;
	}
</style>