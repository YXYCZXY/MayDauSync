<template>
    <view class="container">
        <scroll-view scroll-y :style="'width:690rpx;height:' + list_height + 'rpx'">
            <block v-for="(item, index) in devicesList" :key="index">
                <view class="list-item" :id="item.deviceId" @tap="Connect">
                    <view style="display: flex; flex-direction: column; width: 80%">
                        <text style="font-size: medium; word-break: break-all;color: #fff;">设备名称: {{ item.name }}</text>
                        <text style="font-size: x-small; color: #fff; word-break: break-all">设备ID: {{ item.deviceId }}</text>
                        <text style="font-size: x-small; color: #fff; word-break: break-all">信号强度RSSI: {{ item.RSSI }}</text>
                    </view>
                    <image style="width: 36px; height: 36px" mode="aspectFit" src="/static/images/bluetooth.png"></image>
                </view>
            </block>
        </scroll-view>
        <button type="primary" class="button" :loading="searching" @tap="Search">{{ searching ? '搜索中...' : '搜索蓝牙设备' }}</button>
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
					
					app.globalData.deviceId = e.currentTarget.id;
					uni.setStorageSync('deviceId', e.currentTarget.id);
                    uni.navigateTo({
                        url: '../index/index'
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
page {
    background-color: #f8f8f8;
}
.container {
    padding: 0 30rpx 0 30rpx;
    align-items: center;
	background-image: url('https://6d61-maydaysync-2gaijzhh7553fabf-1327815928.tcb.qcloud.la/maydayimgs/images/bg/ble.jpg?sign=eadc73c038e302a5ba05f612130a870a&t=1721737460');
	background-size: 100% 100%;
}
.list-item {
    display: flex;
    flex-direction: row;
    justify-content: space-between;
    align-items: center;
    width: 100%;
    padding: 10px 0 10px 0;
    box-sizing: border-box;
    border: 1px solid #000;
    border-style: none none solid none;
    border-bottom-color: lightgray;
}
.list-item:last-child {
    border-style: none;
}
.button {
    position: fixed;
    width: 690rpx;
    bottom: 30rpx;
}
</style>
