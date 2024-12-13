<template>
	<view class="centerBox">
		<view class="box">
			<text class="textBox">登录密码</text>
			<input class="inputBox" placeholder="请输入登录密码" />
			<view class="viewBox" @click="">修改</view>
		</view>
		<view class="box">
			<text class="textBox">密保问题</text>
			<input class="inputBox" placeholder="请输入密保问题" />
			<view class="viewBox" @click="">设置</view>
		</view>
		<view class="box">
			<text class="textBox">安全手机</text>
			<input class="inputBox" placeholder="请输入安全手机号" />
			<view class="viewBox" @click="">修改</view>
		</view>
		<view class="box">
			<text class="textBox">安全邮箱</text>
			<input class="inputBox" placeholder="请输入安全邮箱" />
			<view class="viewBox" @click="">修改</view>
		</view>
	</view>
</template>

<script setup>
import { ref } from 'vue';

</script>

<style lang="scss" scoped>
.centerBox{
	width: 100%;
	height: 100%;
	padding: 20px;
	.box{
		width: 100%;
		display: flex;
		align-items: center; // 垂直居中对齐
		
		.inputBox{
			width: 86%;
		}
		.viewBox{
			width: 3%;
			color: #165dff;
		}
		.textBox{
			width: 7%;
			margin: 1%;

		}
	}
}


</style>