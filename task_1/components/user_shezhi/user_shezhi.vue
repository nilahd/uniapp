<!-- menu_id: 42 -->
<template>
	<view class="user">
		<view class="user-top">

			<view class="left">
				<image src="../../static/logo.png" mode="aspectFill" style="margin-bottem:3px;margin-top: 3px;height: 90%;width: 60%;border-radius: 50%;margin-left: 20px;"></image>
				<view style="background-color: #c7ffff;position: absolute;width: 2%;height: 23%;bottom: 10%;left: 9%;z-index:2;border-radius: 50%;">
					<image src="../../static/camera.png" mode="aspectFill" style="display: flex;position: absolute;width: 70%;height: 70%;justify-content: center;align-items: center;margin: 15%;"></image>
				</view>           
			</view>
			<view class="right">
				<text>用户名 : 王立群{{}}</text>
				<text>实名认证 : 已认证{{}}</text>
				<text>账号ID : 15012312300{{}}</text>
				<text>手机号码 : 150****0000{{}}</text>
				<text>注册时间 : 2013-05-10 12:10:00{{}}</text>
			</view>
			
		</view>
		<view class="user-bottom">
			<view class="top">
			  <button type="primary" @click="showComponent('basicInfo')">基础信息</button>
			  <button type="primary" @click="showComponent('securitySettings')">安全设置</button>
			  <button type="primary" @click="showComponent('realNameAuth')">实名认证</button>
			</view>
			<view class="bottom">
					<component :is="currentComponent"></component>
			</view>
		</view>
	</view>
</template>

<script setup>
import BasicInfo from '../BasicInfo/BasicInfo.vue'; 
import SecuritySettings from '../SecuritySettings/SecuritySettings.vue'; 
import RealNameAuth from '../RealNameAuth/RealNameAuth.vue'; 
import { onMounted, ref } from 'vue';

const basicInfoComponent = BasicInfo;
const securitySettingsComponent = SecuritySettings;
const realNameAuthComponent = RealNameAuth;

const currentComponent = ref(basicInfoComponent);
const showComponent = (componentName) => {
  switch (componentName) {
    case 'basicInfo':
      currentComponent.value = basicInfoComponent;
      break;
    case'securitySettings':
      currentComponent.value = securitySettingsComponent;
      break;
    case'realNameAuth':
      currentComponent.value = realNameAuthComponent;
      break;
  }
};
</script>

<style lang="scss" scoped>
* {
	margin: 0;
	padding: 0;
	box-sizing: border-box;
}
.user{
	width: 100%;
	height: 100%;
	display: flex;
	flex-direction: column;
	.user-top{
		position: relative;

		.left{
			// border: 1px solid red;
			width: 13%;
			height: 100%;
			display: flex;
			justify-content: center;
			align-items: center;
		}
		.right{
			justify-content: center;
			align-items: center;
			display: grid;
			// border: 1px solid blue;
			height: 100%;
			padding: 15px; /* 减小内边距 */

			// width: 87%;
			width: 40%;
			grid-template-columns: repeat(2, 1fr);
			margin-left: 30px;
			text{
				width: 100%;
				max-height: 90%;
				overflow: hidden;
				-webkit-user-select: text;
				-moz-user-select: text;
				-ms-user-select: text;
				user-select: text;
				font-size: 13px;
			}
		}
		display: flex;
		// border: 1px solid black;
		margin-top: 10px;
		margin-left: 10px;
		width: 99%;
		height: 16%;
		border-radius: 5px;
	}
	.user-bottom{
		// border: 1px solid black;
		margin-top: 10px;
		margin-left: 10px;
		width: 99%;
		height: 80%;
		border-radius: 5px;
		.top{
			margin-top: 20px;
			margin-left: 10px;
			display: flex;
			button{
				margin-left: 10px;
				width: 100px;
				height: 30px;
				font-size: 12px;
				border-radius: 20px;
			}
		}
		.bottom{
			margin-top: 20px;
			width: 100%;
			height: 80%;
			display: flex;
			justify-content: center;
			align-items: center;

		}
	}
}




</style>