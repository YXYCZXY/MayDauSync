<template>
	<view class="content head-bg" >
		<view class="text-area">
			<uni-row gutter="20" class="demo-uni-row" :width="nvueWidth">
				<uni-col :span="8" v-for="url in wmls" :key="url">
					<sync-card title="胡胡胡罗"  :src="url"></sync-card>
				</uni-col>
			</uni-row>
		</view>
		<TabBar class="bom-tabbar" :pagePath="'/pages/Ashin/index'"></TabBar>
	</view>
</template>

<script>
	export default {
		options: {
			styleIsolation: 'shared'
		},
		data() {
			return {
				wmls:[
					'https://6d61-maydaysync-2gaijzhh7553fabf-1327815928.tcb.qcloud.la/maydayimgs/images/Ashin/wmls/wmls4.png?sign=84e2ea5a6171caa96c4104947f385f8f&t=1720972858',
					'https://6d61-maydaysync-2gaijzhh7553fabf-1327815928.tcb.qcloud.la/maydayimgs/images/Ashin/wmls/wmls3.png?sign=3d4f10d6f8c3697f551cc844cd7c09c4&t=1720972870',
					'https://6d61-maydaysync-2gaijzhh7553fabf-1327815928.tcb.qcloud.la/maydayimgs/images/Ashin/wmls/wmls2.png?sign=028ad74ca2fc7ead6c05423e1f007d7f&t=1720972881',
					'https://6d61-maydaysync-2gaijzhh7553fabf-1327815928.tcb.qcloud.la/maydayimgs/images/Ashin/wmls/wmls1.png?sign=b56ca9bf8420dabfba052e2a475bf6b0&t=1720972888'
				]
			}
		},
		onLoad() {

		},
		methods: {

		}
	}
</script>

<style>
	.uni-col{
		margin-bottom: 10px;
	}
	.card--uni-card{
		background-color: rgba(255,255,255,0.5);
	}
	.content {
		padding: 10px;
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
		padding: 10px;
		position: absolute;
		top: 200rpx;
		height: 100%;
	}

	.title {
		font-size: 36rpx;
		color: #8f8f94;
	}
	.head-bg{
	  height: 100vh;
/* 	  background-image: url('https://6d61-maydaysync-2gaijzhh7553fabf-1327815928.tcb.qcloud.la/maydayimgs/images/bg/ashin.jpg?sign=2928ff9272259a84b96506fe0716a394&t=1720705784');
	  background-size: 100% 100%; */
	}
</style>
