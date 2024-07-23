<script>
	//app.js
	export default {
		data() {
			return {};
		},
		onLaunch: function() {
			// 展示本地存储能力
			var logs = uni.getStorageSync('logs') || [];
			logs.unshift(Date.now());
			uni.setStorageSync('logs', logs);
			this.globalData.SystemInfo = uni.getSystemInfoSync();
			this.globalData.userInfo = uni.getStorageSync('userInfo') || null;
			this.globalData.SystemInfo = uni.getStorageSync('SystemInfo') || this.globalData.SystemInfo;
			this.globalData.deviceId = uni.getStorageSync('deviceId') || null;
			this.globalData.serviceId = uni.getStorageSync('serviceId') || null;
			this.globalData.notifyUuid = uni.getStorageSync('notifyUuid') || null;
			this.globalData.writeUuid = uni.getStorageSync('writeUuid') || null;
			// 登录
			uni.login({
				success: (res) => {
					// 发送 res.code 到后台换取 openId, sessionKey, unionId
				}
			});
			// 获取用户信息
			uni.getSetting({
				success: (res) => {
					if (res.authSetting['scope.userInfo']) {
						// 已经授权，可以直接调用 getUserInfo 获取头像昵称，不会弹框
						uni.getUserInfo({
							success: (res) => {
								// 可以将 res 发送给后台解码出 unionId
								this.globalData.userInfo = res.userInfo;

								// 由于 getUserInfo 是网络请求，可能会在 Page.onLoad 之后才返回
								// 所以此处加入 callback 以防止这种情况
								if (this.userInfoReadyCallback) {
									this.userInfoReadyCallback(res);
								}
							}
						});
					}
				}
			});
		},
		globalData: {
			userInfo: null,
			SystemInfo: {},
			deviceId: null,
			serviceId: null,
			notifyUuid: null,
			writeUuid: null,
			buf2hex: function(buffer) {
				return Array.prototype.map.call(new Uint8Array(buffer), (x) => ('00' + x.toString(16)).slice(-2)).join(
					'');
			},

			buf2string: function(buffer) {
				var arr = Array.prototype.map.call(new Uint8Array(buffer), (x) => x);
				var str = '';
				for (var i = 0; i < arr.length; i++) {
					str += String.fromCharCode(arr[i]);
				}
				return str;
			}
		}
	};
</script>
<style>
	body {
		height: 100%;
	}

	/**app.wxss**/
	.container {
		height: 100%;
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: space-between;
		padding: 200rpx 0;
		box-sizing: border-box;
	}

	.title {
		position: absolute;
		top: 50%;
		left: 50%;
		transform: translate(-50%, -50%);
		text-align: center;
	}
</style>