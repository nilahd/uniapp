<!-- <template>
  <div ref="container" style="height: 100%; width: 100%;"></div>
</template>

<script setup>
import { ref, onMounted, onBeforeUnmount } from 'vue';
import * as echarts from 'echarts';
import axios from 'axios';

const container = ref(null);
const LOCAL_JSON_PATH = '../static/guangzhou.json'; // 确保路径正确

const data = [
  { name: '荔湾区', value: 123.83 },
  { name: '越秀区', value: 103.87 },
  { name: '海珠区', value: 181.9 },
  { name: '天河区', value: 224.18 },
  { name: '白云区', value: 374.3 },
  { name: '黄埔区', value: 126.44 },
  { name: '番禺区', value: 265.84 },
  { name: '花都区', value: 164.24 },
  { name: '南沙区', value: 84.66 },
  { name: '从化区', value: 71.77 },
  { name: '增城区', value: 146.63 },
];

let myChart = null;

const initChart = (jsonData) => {
  if (myChart) {
    myChart.dispose();
  }
  
  myChart = echarts.init(container.value);
  myChart.showLoading();

  echarts.registerMap('GZ', jsonData);
  
  const mapOption = {
    title: { text: "广州各地区地图" },
    visualMap: {
      left: 'right',
      min: 50,
      max: 400,
      inRange: {
        color: ['#313695', '#4575b4', '#74add1', '#abd9e9', '#e0f3f8', '#ffffbf', '#fee090', '#fdae61', '#f46d43', '#d73027', '#a50026']
      },
      text: ['High', 'Low'],
      calculable: true
    },
    grid: {
      width: '90%',
      top: '10%',
      left: 'center',
      bottom: '10%'
    },
    tooltip: {},
    toolbox: {
      show: true,
      left: 'left',
      top: 'bottom',
      feature: {
        // 隐藏功能
      }
    },
    series: [{
      name: '常住人口(万人)',
      id: 'population',
      type: 'map',
      roam: true,
      map: 'GZ',
      animationDurationUpdate: 1000,
      universalTransition: true,
      data: data
    }]
  };

  myChart.setOption(mapOption);
  myChart.hideLoading();
};

onMounted(() => {
  console.log(container.value); // 检查容器元素是否已被正确挂载

  axios.get(LOCAL_JSON_PATH)
    .then(response => {
      initChart(response.data);
    })
    .catch(error => {
      console.error('Failed to load JSON:', error);
    });

  const resizeChart = () => {
    if (myChart) myChart.resize();
  };

  window.addEventListener('resize', resizeChart);

  // 直接在 onBeforeUnmount 中清理事件监听
  onBeforeUnmount(() => {
    window.removeEventListener('resize', resizeChart);
    if (myChart) {
      myChart.dispose(); // 清理 ECharts 实例
      myChart = null; // 将实例置为 null
    }
  });
});
</script>

<style>
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}
html, body {
  height: 100%;
}
</style> 
 -->

<!-- <template>
  <div ref="container" style="height: 100%; width: 100%;"></div>
</template>

<script setup>
import { ref, onMounted, onBeforeUnmount, nextTick } from 'vue';
import * as echarts from 'echarts';
import axios from 'axios';

const container = ref(null);
const LOCAL_JSON_PATH = 'https://geo.datav.aliyun.com/areas_v3/bound/440100_full.json';

const data = [
  { name: '荔湾区', value: 123.83 },
  { name: '越秀区', value: 103.87 },
  { name: '海珠区', value: 181.9 },
  { name: '天河区', value: 224.18 },
  { name: '白云区', value: 374.3 },
  { name: '黄埔区', value: 126.44 },
  { name: '番禺区', value: 265.84 },
  { name: '花都区', value: 164.24 },
  { name: '南沙区', value: 84.66 },
  { name: '从化区', value: 71.77 },
  { name: '增城区', value: 146.63 },
];

let myChart = null;

const initChart = async (jsonData) => {
  await nextTick();

  if (!container.value) {
    console.error('Container element not available for ECharts initialization.');
    return;
  }

  if (myChart) {
    console.log('Disposing old chart instance.');
    myChart.dispose();
    myChart = null; // 明确设置为 null，确保没有残留引用
  }
  myChart = echarts.init(container.value);
  
  myChart.showLoading();

  echarts.registerMap('GZ', jsonData);
  
  const mapOption = {
    title: { text: "广州各地区地图" },
    visualMap: {
      left: 'right',
      min: 50,
      max: 400,
      inRange: {
        color: ['#313695', '#4575b4', '#74add1', '#abd9e9', '#e0f3f8', '#ffffbf', '#fee090', '#fdae61', '#f46d43', '#d73027', '#a50026']
      },
      text: ['High', 'Low'],
      calculable: true
    },
    grid: {
      width: '90%',
      top: '10%',
      left: 'center',
      bottom: '10%'
    },
    tooltip: {},
    toolbox: {
      show: true,
      left: 'left',
      top: 'bottom',
      feature: {}
    },
    series: [{
      name: '常住人口(万人)',
      id: 'population',
      type: 'map',
      roam: true,
      map: 'GZ',
      animationDurationUpdate: 1000,
      universalTransition: true,
      data: data
    }]
  };
  console.log(mapOption)
  // 报错位置
  myChart.setOption(mapOption);
   
  myChart.hideLoading();
 
  console.log('Chart initialized successfully.');

};

// 定义 resizeChart 函数
const resizeChart = () => {
  if (myChart) {
    console.log('Resizing chart.');
    myChart.resize();
  } else {
    console.log('Chart instance not found during resize.');
  }
};

onMounted(async () => {
  await nextTick();

  // 检查容器是否已挂载
  if (!container.value) {
    console.error('Container element is not available.');
    return;
  }

  try {
    const response = await axios.get(LOCAL_JSON_PATH);
    if (response.data) {
	  console.log(response.data)
		
      await initChart(response.data);
	  
    } else {
      console.error('Empty JSON data received.');
    }
  } catch (error) {
    console.error('Failed to load JSON:', error);
  }

  window.addEventListener('resize', resizeChart);
});

onBeforeUnmount(() => {
  window.removeEventListener('resize', resizeChart);
  if (myChart) {
    myChart.dispose();
    myChart = null;
  }
});
</script>

<style>
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}
html, body {
  height: 100%;
}
</style> -->
<template>
  <div ref="container" style="height: 100%; width: 100%;"></div>
</template>

<script setup>
import { ref, onMounted, onBeforeUnmount, nextTick } from 'vue';
import * as echarts from 'echarts';
import axios from 'axios';

const container = ref(null);
const LOCAL_JSON_PATH = '../../static/guangzhou.json';

const data = [
  { name: '荔湾区' },
  { name: '越秀区' },
  { name: '海珠区' },
  { name: '天河区' },
  { name: '白云区' },
  { name: '黄埔区' },
  { name: '番禺区' },
  { name: '花都区' },
  { name: '南沙区' },
  { name: '从化区' },
  { name: '增城区' },
];

let myChart = null;

const initChart = async (jsonData) => {
  await nextTick();

  if (!container.value) {
    console.error('Container element not available for ECharts initialization.');
    return;
  }

  if (myChart) {
    console.log('Disposing old chart instance.');
    myChart.dispose();
    myChart = null; // 明确设置为 null，确保没有残留引用
  }
  myChart = echarts.init(container.value);

  myChart.showLoading();

  echarts.registerMap('GZ', jsonData);

  const mapOption = {
    title: { text: "广州" },
    visualMap: {
      show: false, // 不显示视觉映射组件
      min: 0,
      max: 1,
      inRange: {
        color: ['#3493e6'] // 浅蓝色
      }
    },
    tooltip: {
      trigger: 'item',
      formatter: '{b}'
    },
    series: [{
      name: '广州市',
      type: 'map',
      roam: true,
      map: 'GZ',
      label: {
        show: true, // 显示标签
        color: '#fff' // 标签文字颜色
      },
      data: data.map(item => ({...item, value: 1})) // 给每个区一个相同的值
    }]
  };
// 错误位置
  myChart.setOption(mapOption);

  myChart.hideLoading();

  console.log('Chart initialized successfully.');
};

const resizeChart = () => {
  if (myChart) {
    console.log('Resizing chart.');
    myChart.resize();
  } else {
    console.log('Chart instance not found during resize.');
  }
};

onMounted(async () => {
	
  await nextTick();
  if (!container.value) {
    console.error('Container element is not available.');
    return;
  }

  try {
    const jsonData = await import(LOCAL_JSON_PATH);
    if (jsonData.default) {
      await initChart(jsonData.default);
    } else {
      console.error('JSON data does not have a default export.');
    }
  } catch (error) {
    console.error('Failed to load JSON:', error);
  }

  window.addEventListener('resize', resizeChart);
});

onBeforeUnmount(() => {
  window.removeEventListener('resize', resizeChart);
  if (myChart) {
    myChart.dispose();
    myChart = null;
  }
});
</script>

<style>
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}
html, body {
  height: 100%;
}
</style>