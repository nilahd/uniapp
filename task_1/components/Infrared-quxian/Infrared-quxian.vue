<template>
  <view class="quxian_box">
    <view class="query-container">
      <view class="query-item">
        <text class="text-box">业务 ID :</text>
        <input class="input-box" v-model="localQueryValues.detailId" placeholder="请输入业务 ID" />
        <text class="text-box">账户名称 :</text>
        <input class="input-box" v-model="localQueryValues.username" placeholder="请输入账户名称" />
        <text class="text-box">设备类型 :</text>
        <uni-data-select class="input-box" v-model="data.value" :localdata="data.range" @change="select" />
        <text class="text-box">区局/供电所 :</text>
        <input class="input-box" v-model="localQueryValues.powerSupplyStation" placeholder="请输入区局/供电所" />
        <text class="text-box">变电站名称 :</text>
        <input class="input-box" v-model="localQueryValues.substationName" placeholder="请输入变电站名称" />
        <text class="text-box">馈线长度 :</text>
        <input class="input-box" v-model="localQueryValues.feeder" placeholder="请输入馈线长度" />
        <text class="text-box">工作负责人 :</text>
        <input class="input-box" v-model="localQueryValues.workLeaderName" placeholder="请输入工作负责人" />
        <text class="text-box">巡检班组 :</text>
        <input class="input-box" v-model="localQueryValues.inspectionTeam" placeholder="请输入巡检班组" />
        <text class="text-box">采集时间 :</text>
        <uni-datetime-picker class="input-box" v-model="localQueryValues.dateRange" type="datetimerange" rangeSeparator="至" @maskClick="maskClick" placeholder="请选择采集时间" />
        <text class="text-box">设备名称 :</text>
        <input class="input-box" v-model="localQueryValues.deviceName" placeholder="请输入设备名称" />
        <text class="text-box">配电房名称 :</text>
        <input class="input-box" v-model="localQueryValues.distributionRoomName" placeholder="请输入配电房名称" />
        <text class="text-box">最大温度 :</text>
        <input class="input-box" v-model="localQueryValues.maxTemp" placeholder="请输入最大温度" />
      </view>
    </view>

    <view class="button-box">
      <button class="button" @click="search" type="primary">查询</button>
      <button class="button" @click="reset" type="primary">重置</button>
    </view>
    </view>

    <view class="echarts">
      <l-echart ref="chartRef"></l-echart>
    </view>
</template>

<script setup>
import * as echarts from 'echarts';
import { onMounted, ref, reactive,onBeforeUnmount} from 'vue';

const chartRef = ref(null);
const data = reactive({
  value: null,
  range: [
    { value: 0, text: "开关柜" },
    { value: 1, text: "变压器" },
    { value: 2, text: "电缆" },
    { value: 3, text: "其他" }
  ]
});
const localQueryValues = reactive({
  detailId: '',
  username: '',
  powerSupplyStation: '',
  substationName: '',
  feeder: '',
  workLeaderName: '',
  inspectionTeam: '',
  deviceName: '',
  distributionRoomName: '',
  maxTemp: '',
  dateRange: []
});
const pageSize = ref(10);
let frontPageCurrent = ref(1);
const loading = ref(false);
let environmentData = ref({});
let chartInstance = ref(null);

const search = () => {
  frontPageCurrent.value = 1;
  const requestData = {
    device: {
      deviceType: data.value === null || data.value === undefined ? null : data.range[data.value]?.value,
      deviceName: localQueryValues.deviceName
    },
    current: 1,
    pageSize: pageSize.value,
    detailId: localQueryValues.detailId,
    username: localQueryValues.username,
    powerSupplyStation: localQueryValues.powerSupplyStation,
    substationName: localQueryValues.substationName,
    feeder: localQueryValues.feeder,
    workLeaderName: localQueryValues.workLeaderName,
    inspectionTeam: localQueryValues.inspectionTeam,
    distributionRoomName: localQueryValues.distributionRoomName,
    maxTemp: localQueryValues.maxTemp,
    dateRange: localQueryValues.dateRange.length > 0 ? [
      convertToUtcFormat(localQueryValues.dateRange[0]),
      convertToUtcFormat(localQueryValues.dateRange[1])
    ] : []
  };
  getData(1, requestData);
};

const reset = () => {
  Object.keys(localQueryValues).forEach(key => {
    localQueryValues[key] = '';
  });
  data.value = null;
  frontPageCurrent.value = 1;
  search();
};

const getData = (currentPage = 1, value = '') => {
  loading.value = true;
  uni.request({
    url: 'http://43.139.118.127/api/detail/charts',
    method: 'POST',
    header: {
      'content-type': 'application/json'
    },
    data: value,
    success: (res) => {
      if (res.statusCode === 200) {
        const responseData = res.data;
        if (responseData.code === 0) {
          environmentData.value = responseData.data;
          console.log(environmentData.value);
          initChart();
        } else {
          console.error('请求失败', responseData.msg);
        }
        loading.value = false;
      } else {
        console.error('请求失败');
        loading.value = false;
      }
    },
    fail: (err) => {
      console.error(err);
      loading.value = false;
    }
  });
};

const option = reactive({
  tooltip: {
    trigger: 'axis'
  },
  legend: {
    data: []
  },
  grid: {
    left: '3%',
    right: '3%',
    bottom: '5%',
    top: '3%',
    containLabel: true
  },
  xAxis: {
    type: 'category',
    data: []
  },
  yAxis: {
    type: 'value'
  },
  series: []
});

const initChart = () => {
  option.legend.data = environmentData.value.seriesDataList.map(item => item.name);
  option.xAxis.data = environmentData.value.categories;
  option.series = environmentData.value.seriesDataList.map(seriesItem => ({
    ...seriesItem,
    smooth: true,
  }));
  chartRef.value.setOption(option);
};

function convertToUtcFormat(dateStr, timezoneOffsetHours = 8) {
  const date = new Date(dateStr);
  const utcDate = new Date(date.getTime() - (timezoneOffsetHours * 60 * 60 * 1000));
  const year = utcDate.getUTCFullYear();
  const month = ("0" + (utcDate.getUTCMonth() + 1)).slice(-2);
  const day = ("0" + utcDate.getUTCDate()).slice(-2);
  const hours = ("0" + utcDate.getUTCHours()).slice(-2);
  const minutes = ("0" + utcDate.getUTCMinutes()).slice(-2);
  const seconds = ("0" + utcDate.getUTCSeconds()).slice(-2);
  const milliseconds = ("00" + utcDate.getUTCMilliseconds()).slice(-3);

  return `${year}-${month}-${day}T${hours}:${minutes}:${seconds}.${milliseconds}Z`;
}

const maskClick = (e) => {
  const startTime = convertToUtcFormat(localQueryValues.dateRange[0]);
  const endTime = convertToUtcFormat(localQueryValues.dateRange[1]);
};

onMounted(async () => {
  await search(); // 确保 search 是一个返回 Promise 的异步函数

  if (chartRef.value) {
    const myChart = await chartRef.value.init(echarts);
    myChart.setOption(option);
  }
});
onBeforeUnmount(() => {
  // 在切换页面时清空图表数据
  environmentData.value = {};
  option.legend.data = [];
  option.xAxis.data = [];
  option.series = [];
  if (chartInstance.value) {
    chartRef.value.dispose(); // 如果有dispose方法则调用，否则忽略
    chartRef.value.setOption(option);
  }
});
</script>

<style lang="scss" scoped>
.echarts {
  position: relative;
  width: 95%;
  height: 55vh;
  border: 1px solid black;
  border-radius: 10px;
  margin-left: 2.5%;
  overflow: hidden;

 .l-echart {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
  }
}

.query-container {
  display: grid;
  grid-template-columns: repeat(8, 1fr);

 .query-item {
    display: grid;
    grid-template-columns: repeat(8, 1fr);
    gap: 5px;
    flex-direction: row;
    align-items: center;

   .text-box {
      width: 100px;
      font-size: 16px;
      margin-right: 5px;
      align-items: center;
      margin-top: 5px;
    }

   .input-box {
      height: 35px;
      width: 220px;
      margin-top: 5px;
      border: 1px solid #d0d0d0;
      border-radius: 5px;
      align-items: center;
      box-shadow: 0 0 3px #b8b8b8;
    }

   .input-box1 {
      box-shadow: 0 0 3px #0fc7ff;
    }
  }
}

.button-box {
  margin-top: 30px;
  margin-bottom: 30px;
  margin-left: 2.5%;
  display: flex;
  justify-content: flex-start;
  gap: 30px;
  width: 250px;

 .button {
    width: 80px;
    height: 30px;
    font-size: 16px;
    display: flex;
    align-items: center;
    justify-content: center;
    flex-grow: 1;
    flex-shrink: 1;
  }
}
</style>