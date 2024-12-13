<template>
  <view class="box">
    <view class="form-item">
      <text>姓名：</text>
      <input type="text" v-model="formData.name" placeholder="请输入姓名" />
      <text v-if="errors.name" class="error-message">{{ errors.name }}</text>
    </view>
    <view class="form-item">
      <text>年龄：</text>
      <input type="text" v-model="formData.age" placeholder="请输入年龄" />
      <text v-if="errors.age" class="error-message">{{ errors.age }}</text>
    </view>
    <view class="form-item">
      <text>性别：</text>
      <input type="text" v-model="formData.sex" placeholder="请输入性别" />
      <text v-if="errors.sex" class="error-message">{{ errors.sex }}</text>
    </view>
    <view class="form-item">
      <text>邮箱：</text>
      <input type="text" v-model="formData.email" placeholder="请输入邮箱" />
      <text v-if="errors.email" class="error-message">{{ errors.email }}</text>
    </view>
    <view class="form-item">
      <text>自我介绍：</text>
      <textarea v-model="formData.introduction" placeholder="请输入自我介绍" />
    </view>
    <view class="button-box">
      <button @click="submitForm" type="primary">保存</button>
      <button @click="resetForm">重置</button>
    </view>
  </view>
</template>

<script setup>
import { ref } from 'vue';

const formData = ref({
  name: '',
  age: '',
  sex: '',
  email: '',
  introduction: ''
});

const errors = ref({
  name: '',
  age: '',
  email: ''
});


const validateForm = () => {
  let valid = true;
  const nameRegex = /^[\u4e00-\u9fa5a-zA-Z0-9]+$/;
  const ageRegex = /^\d+$/;
  const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;

  if (!formData.value.name ||!nameRegex.test(formData.value.name)) {
    errors.value.name = '请输入正确的姓名';
    valid = false;
  } else {
    errors.value.name = '';
  }

  if (!formData.value.age ||!ageRegex.test(formData.value.age)) {
    errors.value.age = '请输入正确的年龄';
    valid = false;
  } else {
    errors.value.age = '';
  }
  // 性别验证
  if (formData.value.sex !== '男' && formData.value.sex !== '女') {
    errors.value.sex = '请输入正确的性别（男/女）';
    valid = false;
  } else {
    errors.value.sex = '';
  }
  if (!formData.value.email ||!emailRegex.test(formData.value.email)) {
    errors.value.email = '请输入正确的邮箱格式';
    valid = false;
  } else {
    errors.value.email = '';
  }

  return valid;
};

const submitForm = () => {
  if (validateForm()) {
    // 在这里转换性别值
    formData.value.sex = formData.value.sex === '男' ? '0' : '1';
    
    console.log('提交的数据：', formData.value);
    // 表单提交成功后可以选择重置表单
    resetForm();
  } else {
    console.log('表单验证不通过');
    // 不调用 resetForm()，保持表单内容
  }
};

const resetForm = () => {
  formData.value = {
    name: '',
    age: '',
    sex: '',
    email: '',
    introduction: ''
  };
  errors.value = {
    name: '',
    age: '',
    sex: '',
    email: ''
  };
};
</script>

<style lang="scss" scoped>
.box {
  width: 500px;
  padding: 20px;
  border-radius: 10px;

 .form-item {
    margin-bottom: 15px;
    display: flex;
    align-items: center;
  }

  input,
  textarea {
    flex: 1;
    padding: 8px;
    border: 1px solid #ccc;
    border-radius: 5px;
    &:focus {
      outline: none;
      border-color: #007bff;
    }
  }

 .button-box {
    display: flex;
    justify-content: space-between;
    margin-top: 20px;
  }

  button {
    flex: 1;
    margin-right: 10px;
    &:last-child {
      margin-right: 0;
    }
  }

 .error-message {
    color: red;
    font-size: 12px;
    margin-top: 5px;
  }
}
</style>