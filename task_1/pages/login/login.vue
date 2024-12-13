<template>
  <view class="box">
    <!-- 左侧部分 -->
    <view class="left">
      <view class="centerBox">
        <!-- 公司 logo -->
        <image src="../../static/logo_nf.png" alt="" />
        <view>
          <!-- 系统名称 -->
          <text class="h4">图谱后台管理系统</text>
        </view>
      </view>
    </view>
    <!-- 右侧部分 -->
    <view class="right">
      <form>
        <!-- 欢迎登录标题 -->
        <text class="h3">欢迎登录</text>
        <!-- 输入账号的输入框 -->
        <input type="text" placeholder="请输入账号" required v-model="userName" />
        <!-- 输入密码的输入框 -->
        <input type="password" placeholder="请输入密码" required v-model="password" />
        <view style="display: flex; align-items: flex-start;">
          <!-- 输入验证码的输入框 -->
          <input type="text" placeholder="请输入验证码" required v-model="verificationCode" style="width: 70%; margin-right: 20px;" />
          <!-- 刷新验证码的区域 -->
          <view @click="refreshVerificationCode" style="background-color: lightgray; padding: 5px; width: 100px; display: flex; justify-content: space-around;">
            <text>{{ verificationCodeText }}</text>
          </view>
        </view>
        <!-- 登录按钮 -->
        <button type="button" class="loginBtn" @click="login">登录</button>
        <!-- 注册链接 -->
        <view class="no" @click="register">没有账号？立即注册</view>
      </form>
    </view>
  </view>
</template>

<script setup>
import { ref } from 'vue';
import { useRouter } from 'vue-router';

// 用户名，使用 ref 创建响应式数据
const userName = ref("");
// 密码，使用 ref 创建响应式数据
const password = ref("");
// 验证码，使用 ref 创建响应式数据
const verificationCode = ref("");
// 默认验证码，使用 ref 创建响应式数据，并初始化为随机生成的验证码
const verificationCodeText = ref(generateVerificationCode()); 

const router = useRouter();

// 生成随机验证码的函数
function generateVerificationCode() {
  let code = "";
  for (let i = 0; i < 4; i++) {
    code += Math.floor(Math.random() * 10);
  }
  return code;
}

// 刷新验证码的函数
function refreshVerificationCode() {
  verificationCodeText.value = generateVerificationCode();
}

// 登录逻辑函数
function login() {
  // 判断用户名、密码和验证码是否都有输入
  if (!userName.value || !password.value || !verificationCode.value) {
    uni.showToast({
      title: '请输入完整信息',
      icon: 'none',
      duration: 2000
    });
    return;
  }

  // 构造表单数据
  const boundary = '----WebKitFormBoundaryNThFtUYSpSLSAlld';
  let formData = '';
  formData += `--${boundary}\r\n`;
  formData += 'Content-Disposition: form-data; name="userName"\r\n\r\n';
  formData += `${userName.value}\r\n`;
  formData += `--${boundary}\r\n`;
  formData += 'Content-Disposition: form-data; name="password"\r\n\r\n';
  formData += `${password.value}\r\n`;
  formData += `--${boundary}--\r\n`;

  // 发送登录请求
  uni.request({
    url: 'http://43.139.118.127/api/login',
    method: 'POST',
    header: {
      'Content-Type': `multipart/form-data; boundary=${boundary}`
    },
    data: formData,
    success: (res) => {
      console.log(res);
      // 判断登录是否成功
      if (res.data.code === 0) {
        uni.showToast({
          title: '登录成功',
          icon: 'success',
          duration: 2000
        });
        // 延迟 2 秒后跳转到首页
        setTimeout(() => {
          router.push('/pages/index/index');
        }, 2000);
      } else {
        uni.showToast({
          title: res.data.msg || '登录失败',
          icon: 'none',
          duration: 2000
        });
      }
    },
    fail: (err) => {
      uni.showToast({
        title: '请求失败，请检查网络',
        icon: 'none',
        duration: 2000
      });
      console.log('登录请求失败', err);
    }
  });
}
// 跳转到注册页面的逻辑函数
function register() {
  router.push('/pages/enroll/enroll');
}
</script>

<style lang="scss">
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

.box {
  height: 100vh;
  background-color: aquamarine;
  background: url("https://images.pexels.com/photos/936722/pexels-photo-936722.jpeg?auto=compress&cs=tinysrgb&w=1260&h=750&dpr=1") no-repeat;
  background-size: cover;
  display: flex;
}

.left {
  position: relative;
  width: 100%;
  height: 100%;
  background-color: rgba(57, 99, 134, 0.75);
}

.right {
  position: relative;
  width: 50%;
  height: 100%;
  background-color: rgba(255, 255, 255, 1);
}

.centerBox {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  width: 80%;
  text-align: center;
}

.left image {
  width: 100px;
  height: 100px;
  margin-bottom: 30px;
}

.left text {
  font-size: 40px;
  color: #fff;
}

.left.h4 {
  font-size: 18px;
  color: #fff;
  margin-bottom: 10px;
}

.right form {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  width: 80%;
  text-align: center;
}

.h3 {
  margin-bottom: 20px;
  font-size: 20px;
}

input {
  width: 100%;
  height: 30px;
  border: 1px solid #767676;
  background-color: transparent;
  padding-left: 10px;
  font-size: 12px;
  color: #000000;
  margin-bottom: 15px;
  outline: none;
}

.loginBtn {
  width: 100%;
  height: 35px;
  line-height: 32px;
  text-align: center;
  font-size: 15px;
  color: #fff;
  border-radius: 6px;
  background: rgb(57, 99, 134);
  outline: none;
  border: none;
  margin-top: 10px;
}

.no {
  cursor: pointer;
  margin-top: 30px;
  text-align: center;
  font-size: 12px;
  color: #828282;
}
</style>