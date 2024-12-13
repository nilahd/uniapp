<template>
    <view class="bch-header">
        <view class="header-left">
			
			<text class="text">后台管理系统</text>
			
		</view>
		
		<view class="header-center">			
				<image class="icon" src="../../static/angle-right.png"></image>
				<view class="text-right">{{menuNameF}}</view>			
		</view>
		
		
		<view class="header-right">
				<image src="../../static/user.png" mode="aspectFill" style="width: 30px; height: 30px;margin-right: 10px;justify-content: center;align-items:center"></image>
					<text class="text" @click="toUser">个人中心</text>

				
		</view>
    </view>
    <view class="bch-content-box">
        <bch-tree-menu :menuData="menuData" :controlItemRightIcon="['iconfont icon-xiangyou','iconfont icon-youxia']"
            :controlButtonIocn="controlButtonIocn" :onClick="callback">
        </bch-tree-menu>
		<view class="content">
			<view class="content-box">
				<view class="box">
					<component :is="currentComponent || Home"></component>
				</view>
			</view>
		</view>
    </view>
	
</template>

<script setup>
import { ref } from 'vue';
import InfraredTupu  from '../../components/Infrared-tupu/Infrared-tupu.vue'
import InfraredXunjian from '../../components/Infrared-xunjian/Infrared-xunjian.vue'; 
import InfraredQuxian from '../../components/Infrared-quxian/Infrared-quxian.vue'; 
import InfraredDianzheng from '../../components/Infrared-dianzheng/Infrared-dianzheng.vue'; 
import Home from '../../components/home/home.vue'
import user_xinxi from '../../components/user_xinxi/user_xinxi.vue'; 
import user_shezhi from '../../components/user_shezhi/user_shezhi.vue'
import PartialTupu  from '../../components/Partial-tupu/Partial-tupu.vue'
import PartialXunjian from '../../components/Partial-xunjian/Partial-xunjian.vue'; 
import PartialQuxian from '../../components/Partial-quxian/Partial-quxian.vue'; 
import PartialDianzheng from '../../components/Partial-dianzheng/Partial-dianzheng.vue'; 


let currentComponent = ref(null);

const menuNameF = ref("首页") ;
const menuNameZ = ref('') ;
const controlButtonIocn = ref(['iconfont icon-jiantou_xiangzuoliangci', 'iconfont icon-jiantou_xiangyouliangci']);
const itemData = ref(null);
const menuData = ref([
  {
    menu_id: 1,
    parent_id: 0,
    show: false,
    name: '首页',
    leftIcon: 'iconfont icon-leibie',
    rightIconState: true
  },
  {
    menu_id: 2,
    parent_id: null,
    show: false,
    name: '红外巡检',
    menuClass: 'm-father ',
    leftIcon: 'iconfont icon-leibie',
    rightIconState: true
  },
  {
    menu_id: 21,
    parent_id: 2,
    show: false,
    name: '红外巡检工单',
    menuClass: 'm-start-leaf',
    leftIcon: 'iconfont icon-gongsi',
    rightIconState: true
  },
  {
    menu_id: 22,
    parent_id: 2,
    show: false,
    name: '红外曲线数据',
    menuClass: 'm-start-leaf',
    leftIcon: 'iconfont icon-gongsi',
    rightIconState: true
  },
  {
    menu_id: 23,
    parent_id: 2,
    show: false,
    name: '红外点阵数据',
    menuClass: 'm-start-leaf',
    leftIcon: 'iconfont icon-gongsi',
    rightIconState: true
  },
  {
    menu_id: 24,
    parent_id: 2,
    show: false,
    name: '红外图谱数据',
    menuClass: 'm-start-leaf',
    leftIcon: 'iconfont icon-gongsi',
    rightIconState: true
  },
  {
    menu_id: 3,
    parent_id: null,
    show: false,
    name: '局放巡检',
    menuClass: 'm-father',
    leftIcon: 'iconfont icon-peizhi1 custom-left-icon-class',
    rightIconState: true
  },
  {
    menu_id: 31,
    parent_id: 3,
    show: false,
    name: '局放巡检工单',
    menuClass: 'm-end-leaf',
    leftIcon: 'iconfont icon-WEBSITE',
    rightIconState: true
  },
  {
    menu_id: 32,
    parent_id: 3,
    show: false,
    name: '局放曲线数据',
    menuClass: 'm-end-leaf',
    leftIcon: 'iconfont icon-WEBSITE',
    rightIconState: true
  },
  {
    menu_id: 33,
    parent_id: 3,
    show: false,
    name: '局放点阵数据',
    menuClass: 'm-end-leaf',
    leftIcon: 'iconfont icon-WEBSITE',
    rightIconState: true
  },
  {
    menu_id: 34,
    parent_id: 3,
    show: false,
    name: '局放图谱数据',
    menuClass: 'm-end-leaf',
    leftIcon: 'iconfont icon-WEBSITE',
    rightIconState: true
  },
  {
    menu_id: 4,
    parent_id: null,
    show: false,
    name: '个人中心',
    menuClass: 'm-father',
    leftIcon: 'iconfont icon-peizhi1 custom-left-icon-class',
    rightIconState: true
  },
  {
    menu_id: 41,
    parent_id: 4,
    show: false,
    name: '用户信息',
    menuClass: 'm-end-leaf',
    leftIcon: 'iconfont icon-WEBSITE',
    rightIconState: true
  },
  {
    menu_id: 42,
    parent_id: 4,
    show: false,
    name: '用户设置',
    menuClass: 'm-end-leaf',
    leftIcon: 'iconfont icon-WEBSITE',
    rightIconState: true
  }
])

function toUser(){
	// 个人中心
	currentComponent.value = user_xinxi;
	menuNameF.value = "用户信息";// 根据需要修改菜单名称显示
}



function callback(e) {
  // console.log('根据业务逻辑进行处理', e);
	  if (e.parent_id === null) {
	    // 如果点击的是父级组件并且要收起，不更新 currentComponent
	    return;
	  }
    menuNameF.value = e.name;

	  switch (e.name) {
		  case '红外巡检工单':
			currentComponent.value = InfraredXunjian;
			break;
		  case '红外曲线数据':
			currentComponent.value = InfraredQuxian;
			break;
		  case '红外图谱数据':
			currentComponent.value = InfraredTupu;
			break;
		  case '红外点阵数据':
			currentComponent.value = InfraredDianzheng;
			break;
		  case '局放巡检工单':
		  	currentComponent.value = PartialXunjian;
		  	break;
		  case '局放曲线数据':
		  	currentComponent.value = PartialQuxian;
		  	break;
		  case '局放图谱数据':
		  	currentComponent.value = PartialTupu;
		  	break;
		  case '局放点阵数据':
		  	currentComponent.value = PartialDianzheng;
		  	break;
		  case '用户信息':
		  	currentComponent.value = user_xinxi;
		  	break;
		  case '用户设置':
		  	currentComponent.value = user_shezhi;
		  	break;
		  default:
			currentComponent.value = null;
		}
	  return menuNameF.value; 

};

</script>

<style lang="scss" scoped>
	* {
	  margin: 0;
	  padding: 0;
	  box-sizing: border-box;
	}
    page {
        width: 100%;
        height: 100%;
        overflow: hidden;

    }

    .bch-header {
        height: 65px;
        // background: linear-gradient(to right, rgb(28, 12, 255), rgb(255, 255, 255));
		// background: linear-gradient(to right, #504DED,#026cdc);
		background-color: #064dbb;
        color: white;
        display: flex;
        align-items: center;
        font-size: 24px;
        padding: 5px;
        box-sizing: border-box;

		.header-center{
			width: 180px;
			display: flex;
			justify-content: space-between; 
			align-items: center;
			> * { 
			    display: flex;
			    align-items: center;
			}
			.text-right{
				font-size: 20px;
				
			}
			.icon{
				width: 20px;
				height: 20px;
				margin-left: 40px;
				
			}
		}
		.header-left{			
			width: 200px;
			display: flex;
			align-items: center;
			justify-content: center;
			.text{
				color: rgb(255, 255, 255);
				font-weight: 600;
				font-size: 28px;
			}
		}
		.header-right{
			width: 15%;
			display: flex;
			align-items: center;
			justify-content: center;
			margin-left: auto;
			.text{
				color: rgb(255, 255, 255);
				font-weight: 500;
				font-size: 18px;
			}
		}
    }

    .bch-content-box {
        height: calc(100% - 75px);
        display: flex;
		background-color: #e6e6e6;
		
		.content{
			width: 100%;
			height: 100%;
			padding: 30px 30px 30px 30px;
			.content-box{
				background-color: #fff;
				width: 100%;
				height: 100%;
				padding: 20px 20px;
				border-radius: 10px;
			
				.box{
					position: relative; /* 为子元素的定位提供相对基准 */
					overflow: hidden; /* 防止子元素超出显示滚动条等情况 */

					width: 100%;
					height: 100%;

				}
			}
		}

        .bch-flex-box {
            display: flex;
            justify-content: flex-start;
            align-items: flex-start;
            flex-wrap: wrap;
            width: 100%;
            overflow-y: auto;

            .bch-flex-item {
                margin: 15px;
                width: 300px;
                height: 600px;
                box-shadow: 0px 0px 10px 2px darkgray;
                border: 2px solid grey;
                border-radius: 20px;
                padding: 20px;
                overflow: hidden;

                .bch-title {
                    background-color: green;
                    text-align: center;
                    color: white;
                    font-size: 18px;
                    padding: 4px;
                    border-radius: 3px;
                    margin-bottom: 5px;
                }

                .demo {
                    border: 1px solid lightgray;
                    height: 350px;
                    display: block;
                    box-sizing: border-box;
                }

                .bch-explain {
                    border: 1px solid lightgray;
                    width: 100%;
                    height: 200px;
                    margin-top: 8px;
                    border-radius: 5px;
                    padding: 5px;
                    box-sizing: border-box;
                    white-space: pre-wrap;

                    p {
                        margin: 2px;
                        text-indent: 10px;
                    }
                }
            }
        }
    }
</style>