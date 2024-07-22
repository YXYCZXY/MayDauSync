<template>
	<view class="content head-bg">
		<view class="text-area">
			<uni-card title="蓝牙连接">
				<scroll-view scroll-y :style="'width:690rpx;height:' + list_height + 'rpx'">
				    <block v-for="(item, index) in devicesList" :key="index">
				        <view class="list-item" :id="item.deviceId" @tap="Connect">
				            <view style="display: flex; flex-direction: column; width: 80%">
				                <text style="font-size: medium; word-break: break-all">设备名称: {{ item.name }}</text>
				                <text style="font-size: x-small; color: gray; word-break: break-all">设备ID: {{ item.deviceId }}</text>
				                <text style="font-size: x-small; color: gray; word-break: break-all">信号强度RSSI: {{ item.RSSI }}</text>
				            </view>
				            <image style="width: 36px; height: 36px" mode="aspectFit" src="/static/images/bluetooth.png"></image>
				        </view>
				    </block>
				</scroll-view>
				<button type="primary" class="button" :loading="searching" @tap="Search">{{ searching ? '搜索中...' : '搜索蓝牙设备' }}</button>
			</uni-card>

		</view>
		<TabBar class="bom-tabbar" :pagePath="'/pages/Ming/index'"></TabBar>
	</view>
</template>

<script>
const app = getApp();
export default {
    data() {
        return {
            searching: false,
            devicesList: [],
            list_height: ''
        };
    },
	options: {
		styleIsolation: 'shared'
	},
    onLoad: function (options) {
        var that = this;
        var list_height = (app.globalData.SystemInfo.windowHeight - 50) * (750 / app.globalData.SystemInfo.windowWidth) - 60;
        that.setData({
            list_height: list_height
        });
        uni.onBluetoothAdapterStateChange(function (res) {
            console.log(res);
            that.setData({
                searching: res.discovering
            });
            if (!res.available) {
                that.setData({
                    searching: false
                });
            }
        });
        uni.onBluetoothDeviceFound(function (devices) {
            //剔除重复设备，兼容不同设备API的不同返回值
            var isnotexist = true;
            if (devices.deviceId) {
                if (devices.advertisData) {
                    devices.advertisData = app.globalData.buf2hex(devices.advertisData);
                } else {
                    devices.advertisData = '';
                }
                console.log(devices);
                for (var i = 0; i < that.devicesList.length; i++) {
                    if (devices.deviceId == that.devicesList[i].deviceId) {
                        isnotexist = false;
                    }
                }
                if (isnotexist) {
                    that.devicesList.push(devices);
                }
            } else if (devices.devices) {
                if (devices.devices[0].advertisData) {
                    devices.devices[0].advertisData = app.globalData.buf2hex(devices.devices[0].advertisData);
                } else {
                    devices.devices[0].advertisData = '';
                }
                console.log(devices.devices[0]);
                for (var i = 0; i < that.devicesList.length; i++) {
                    if (devices.devices[0].deviceId == that.devicesList[i].deviceId) {
                        isnotexist = false;
                    }
                }
                if (isnotexist) {
                    that.devicesList.push(devices.devices[0]);
                }
            } else if (devices[0]) {
                if (devices[0].advertisData) {
                    devices[0].advertisData = app.globalData.buf2hex(devices[0].advertisData);
                } else {
                    devices[0].advertisData = '';
                }
                console.log(devices[0]);
                for (var i = 0; i < devices_list.length; i++) {
                    if (devices[0].deviceId == that.devicesList[i].deviceId) {
                        isnotexist = false;
                    }
                }
                if (isnotexist) {
                    that.devicesList.push(devices[0]);
                }
            }
            that.setData({
                devicesList: that.devicesList
            });
        });
    },
    onReady: function () {},
    onShow: function () {},
    onHide: function () {
        var that = this;
        that.setData({
            devicesList: []
        });
        if (this.searching) {
            uni.stopBluetoothDevicesDiscovery({
                success: function (res) {
                    console.log(res);
                    that.setData({
                        searching: false
                    });
                }
            });
        }
    },
    methods: {
        Search: function () {
            var that = this;
            if (!that.searching) {
                uni.closeBluetoothAdapter({
                    complete: function (res) {
                        console.log(res);
                        uni.openBluetoothAdapter({
                            success: function (res) {
                                console.log(res);
                                uni.getBluetoothAdapterState({
                                    success: function (res) {
                                        console.log(res);
                                    }
                                });
                                uni.startBluetoothDevicesDiscovery({
                                    allowDuplicatesKey: false,
                                    success: function (res) {
                                        console.log(res);
                                        that.setData({
                                            searching: true,
                                            devicesList: []
                                        });
                                    }
                                });
                            },
                            fail: function (res) {
                                console.log(res);
                                uni.showModal({
                                    title: '提示',
                                    content: '请检查手机蓝牙是否打开',
                                    showCancel: false,
                                    success: function (res) {
                                        that.setData({
                                            searching: false
                                        });
                                    }
                                });
                            }
                        });
                    }
                });
            } else {
                uni.stopBluetoothDevicesDiscovery({
                    success: function (res) {
                        console.log(res);
                        that.setData({
                            searching: false
                        });
                    }
                });
            }
        },

        Connect: function (e) {
            var that = this;
            var advertisData;
            var name;
            console.log(e.currentTarget.id);
            for (var i = 0; i < that.devicesList.length; i++) {
                if (e.currentTarget.id == that.devicesList[i].deviceId) {
                    name = that.devicesList[i].name;
                    advertisData = that.devicesList[i].advertisData;
                }
            }
            uni.stopBluetoothDevicesDiscovery({
                success: function (res) {
                    console.log(res);
                    that.setData({
                        searching: false
                    });
                }
            });
            uni.showLoading({
                title: '连接蓝牙设备中...'
            });
            uni.createBLEConnection({
                deviceId: e.currentTarget.id,
                success: function (res) {
                    console.log(res);
                    uni.hideLoading();
                    uni.showToast({
                        title: '连接成功',
                        icon: 'success',
                        duration: 1000
                    });
                    uni.navigateTo({
                        url: '../device/device?connectedDeviceId=' + e.currentTarget.id + '&name=' + name
                    });
                },
                fail: function (res) {
                    console.log(res);
                    uni.hideLoading();
                    uni.showModal({
                        title: '提示',
                        content: '连接失败',
                        showCancel: false
                    });
                }
            });
        }
    }
};
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
		color: black!important;
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


	.text-area {
		width: 100%;
		height: calc(100% - 120px);
		overflow-x: auto;
		position: absolute;
		/* top: 200rpx; */
	}
	.head-bg {
		height: 100%;
	}
</style>