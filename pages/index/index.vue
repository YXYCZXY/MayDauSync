<template>
    <view>
        <!-- 组件显示 -->
        <view v-if="!(currentTab == 0 ? false : true)">
            <component_Monster />
        </view>
        <view v-if="!(currentTab == 1 ? false : true)">
            <component_Masa />
        </view>
        <view v-if="!(currentTab == 2 ? false : true)">
            <component_Ashin />
        </view>
		<view v-if="!(currentTab == 3 ? false : true)">
		    <component_Stone />
		</view>
		<view v-if="!(currentTab == 4 ? false : true)">
		    <component_Ming />
		</view>

        <!-- 自定义 tabbar -->
        <view class="nav-tabs">
            <view :class="'tab-list '+ (currentTab == idx ? 'active' : 'default')" :data-current="idx" @tap="swichNav" v-for="(item, idx) in items" :key="idx">
                <!-- <text class="tab-text" :data-current="idx" :src="currentTab == idx ? item.selectedIconPath : item.iconPath">{{ item.text }}</text> -->

                <image :class="'iconPath ' + 'bounce' + idx + ' '+ (idx == 2 ? 'bigimg' : '') "  :data-current="idx" :src="currentTab == idx ? item.selectedIconPath : item.iconPath"></image>
            </view>
        </view>
    </view>
</template>

<script>
import component_Monster from '@/pages/component/Monster/index';
import component_Masa from '@/pages/component/Masa/index';
import component_Ashin from '@/pages/component/Ashin/index';
import component_Stone from '@/pages/component/Stone/index';
import component_Ming from '@/pages/component/Ming/index';

let app = getApp();
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
            currentTab: 0,
            items: [
                {
                    iconPath: '/static/pages/img/bar-gs.png',
                    selectedIconPath: '/static/pages/img/bar-gs.png',
                    text: 'Monster'
                },
                {
                    iconPath: '/static/pages/img/bar-ms.png',
                    selectedIconPath: '/static/pages/img/bar-ms.png',
                    text: 'Masa'
                },
                {
                    iconPath: '/static/pages/img/bar-as.png',
                    selectedIconPath: '/static/pages/img/bar-as.png',
                    text: 'Ashin'
                },
                {
                    iconPath: '/static/pages/img/bar-st.png',
                    selectedIconPath: '/static/pages/img/bar-st.png',
                    text: 'Stone'
                },
                {
                    iconPath: '/static/pages/img/bar-gy.png',
                    selectedIconPath: '/static/pages/img/bar-gy.png',
                    text: 'Ming'
                }
            ]
        };
    },
    onLoad: function (option) {},
    methods: {
        swichNav: function (e) {
            let that = this;
            if (this.currentTab === e.target.dataset.current) {
                return false;
            } else {
                that.setData({
                    currentTab: e.target.dataset.current
                });
            }
        }
    }
};
</script>
<style lang="scss" scoped>
	@keyframes bounce0 {
	    0%, 20%, 50%, 80%, 100% {
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
	    0%, 20%, 50%, 80%, 100% {
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
	    0%, 20%, 50%, 80%, 100% {
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
	    0%, 20%, 50%, 80%, 100% {
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
    background: #fcfcfc;
    height: 120rpx;

}

.tab-text {
    font-size: 24rpx;
    line-height: 35rpx;
    color: #5f5f5f;
}

.bigimg{
	width: 74rpx!important;
	height: 74rpx!important;
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
