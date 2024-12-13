<template>
  <view>
    <!-- 查询条件容器 -->
    <view class="query-container">
      <view class="query-item">
        <text class="text-box">业务ID :</text>
        <input class="input-box" v-model="queryValues.detailId" placeholder="请输入业务ID" />
        <text class="text-box">账户名称 :</text>
        <input class="input-box" v-model="queryValues.username" placeholder="请输入账户名称" />
        <text class="text-box">设备类型 :</text>
        <uni-data-select class="input-box" v-model="data.value" :localdata="data.range"/>
        <text class="text-box">区局/供电所 :</text>
        <input class="input-box" v-model="queryValues.powerSupplyStation" placeholder="请输入区局/供电所" />
        <text class="text-box">变电站名称 :</text>
        <input class="input-box" v-model="queryValues.substationName" placeholder="请输入变电站名称" />
        <text class="text-box">馈线长度 :</text>
        <input class="input-box" v-model="queryValues.feeder" placeholder="请输入馈线长度" />
        <text class="text-box">工作负责人 :</text>
        <input class="input-box" v-model="queryValues.workLeaderName" placeholder="请输入工作负责人" />
        <text class="text-box">巡检班组 :</text>
        <input class="input-box" v-model="queryValues.inspectionTeam" placeholder="请输入巡检班组" />
        <text class="text-box">采集时间 :</text>
        <uni-datetime-picker class="input-box" v-model="queryValues.dateRange" type="datetimerange" rangeSeparator="至" @maskClick="maskClick" placeholder="请选择采集时间" />
        <text class="text-box">设备名称 :</text>
        <input class="input-box" v-model="queryValues.deviceName" placeholder="请输入设备名称" />
        <text class="text-box">配电房名称 :</text>
        <input class="input-box" v-model="queryValues.distributionRoomName" placeholder="请输入配电房名称" />
        <text class="text-box">最大温度 :</text>
        <input class="input-box" v-model="queryValues.maxTemp" placeholder="请输入最大温度" />
      </view>
    </view>

    <!-- 按钮组 -->
    <view class="button-box">
      <button class="button" @click="search" type="primary">查询</button>
      <button class="button" @click="reset" type="primary">重置</button>
<!--      <button class="button" @click="download" type="primary">下载</button> -->
<!--      <button class="button-del" @click="del" type="warn">删除</button -->
    </view>

    <!-- 数据表格 -->
    <view class="uni-container">
      <uni-table ref="tableRef" :loading="loading" border stripe type="selection" emptyText="暂无更多数据" @selection-change="selectionChange">
        <uni-tr class="table-title">
          <uni-th width="120" align="center">业务ID</uni-th>
          <uni-th width="80" align="center">用户名称</uni-th>
          <uni-th width="90" align="center">工作负责人</uni-th>
          <uni-th width="100" align="center">区局/供电局</uni-th>
          <uni-th width="100" align="center">变电站名称</uni-th>
          <uni-th width="110" align="center">馈线长度</uni-th>
          <uni-th width="100" align="center">配电房名称</uni-th>
          <uni-th width="100" align="center">巡检班组</uni-th>
          <uni-th width="100" align="center">设备名称</uni-th>
          <uni-th width="80" align="center">设备类型</uni-th>
          <uni-th width="180" align="center">采集时间</uni-th>
        </uni-tr>
        <uni-tr v-for="(item, index) in displayedTableData" :key="index">
          <uni-td>
            <view class="item">{{ item.detailId }}</view>
          </uni-td>
          <uni-td>
            <view class="item">{{ item.username }}</view>
          </uni-td>
          <uni-td>
            <view class="item">{{ item.workLeaderName }}</view>
          </uni-td>
          <uni-td>
            <view class="item">{{ item.powerSupplyStation }}</view>
          </uni-td>
          <uni-td>
            <view class="item">{{ item.substationName }}</view>
          </uni-td>
          <uni-td>
            <view class="item">{{ item.feeder }}</view>
          </uni-td>
          <uni-td>
            <view class="item">{{ item.inspectionTeam }}</view>
          </uni-td>
          <uni-td>
            <view class="item">{{ item.distributionRoomName }}</view>
          </uni-td>
          <uni-td>
            <view class="item">{{ item.deviceName }}</view>
          </uni-td>
          <uni-td>
            <view class="item">{{ item.deviceType }}</view>
          </uni-td>
          <uni-td>
            <view class="item">{{ item.createTime }}</view>
          </uni-td>
        </uni-tr>
      </uni-table>
      <!-- 分页组件 -->
      <view class="uni-pagination-box">
        <uni-pagination show-icon :page-size="frontPageSize" :current="frontPageCurrent" :total="tableData.length" @change="change" />
      </view>
    </view>
  </view>
</template>

<script setup>
import { onMounted, ref, reactive, watch } from 'vue';
import unitable from '../../uni_modules/uni-table/components/uni-table/uni-table.vue'


// 设备类型数据
const data = reactive({
  value: null, // 当前选中的设备类型索引
  range: [ // 设备类型的选项列表
    { value: 0, text: "开关柜" },
    { value: 1, text: "变压器" },
    { value: 2, text: "电缆" },
    { value: 3, text: "其他" }
  ]
});

// 查询条件
const queryValues = reactive({
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
  dateRange:[]
});

const tableRef = ref(null);


// 表格数据
const tableData = ref([]);
const displayedTableData = ref([]);

// 分页参数
const pageSize = ref(50); // 每次从服务器获取50条数据
const frontPageSize = 10; // 前端展示时每页的大小
let frontPageCurrent = ref(); // 前端当前页码

const loading = ref(false); // 加载状态
const selectedIndexs = ref([]); // 选中的行索引

// 获取选中的项目
const selectedItems = () => {
  return selectedIndexs.value.map(i => tableData.value[i]);
};

// 选中行变化事件
const selectionChange = (e) => {
    // 先获取当前选中行对应的detailId数组
    const detailIdArray = e.detail.index.map(index => {
        const item = tableData.value[index];
        if (item && item.detailId) {
            return item.detailId;
        }
        return null;
    }).filter(id => id!== null);

    // 然后将detailId数组赋值给e.detail.index
    e.detail.index = detailIdArray;

    console.log(e.detail.index);

    // 清空已有的选中索引
    selectedIndexs.value = [];

    // 遍历所有选中的索引，并从tableData中找到对应的detailId
    e.detail.index.forEach(detailId => {
        const index = tableData.value.findIndex(item => item.detailId === detailId);
        if (index!== -1) {
            selectedIndexs.value.push({ detailId: detailId, index: index });
        }
    });
};

// 删除选中的项
const delTable = () => {
  console.log(selectedIndexs.value.map(item => item.detailId)); // 输出选中的detailId
};

// 分页改变事件
const change = (e) => {
  
    if (tableRef.value && typeof tableRef.value.clearSelection === 'function') {
      tableRef.value.clearSelection();
    } 

  
  frontPageCurrent = e.current;
  selectedIndexs.value = [];

  renderFrontPage(frontPageCurrent);
};

// 查询功能
const search = () => {
  frontPageCurrent = 1; // 重置当前页码到第一页
  const requestData = {
    device: {
	  deviceType: data.value === null || data.value === undefined ? null : data.range[data.value]?.value,
      deviceName: queryValues.deviceName
    },
    current: 1,
    pageSize: pageSize.value,
    detailId: queryValues.detailId,
    username: queryValues.username,
    powerSupplyStation: queryValues.powerSupplyStation,
    substationName: queryValues.substationName,
    feeder: queryValues.feeder,
    workLeaderName: queryValues.workLeaderName,
    inspectionTeam: queryValues.inspectionTeam,
    distributionRoomName: queryValues.distributionRoomName,
    maxTemp: queryValues.maxTemp,
    dateRange: queryValues.dateRange.length > 0 ? [
      convertToUtcFormat(queryValues.dateRange[0]),
      convertToUtcFormat(queryValues.dateRange[1])
    ] : []
  };
  getData(1, requestData);
  renderFrontPage(frontPageCurrent);
};


const reset = () => {
    Object.keys(queryValues).forEach(key => {
        queryValues[key] = '';
    });
	if (tableRef.value && typeof tableRef.value.clearSelection === 'function') {
	  tableRef.value.clearSelection();
	} 
    data.value = null;
    data.range.value = [];
    selectedIndexs.value = [];
    frontPageCurrent = 1;
    search();
    renderFrontPage(frontPageCurrent); // 渲染第一页
};


// // 下载功能（此处未实现具体逻辑）
// const download = () => {
//   // 实现下载逻辑
// };



const getData = (currentPage = 1, value = '') => {
  loading.value = true;
	
  uni.request({
    url: 'http://43.139.118.127/api/detail/pageVo',
    method: 'POST',
    header: {
      'content-type': 'application/json' // 默认值
    },
    data: value,
    success: (res) => {
      if (res.statusCode === 200) {
        const responseData = res.data;
        if (responseData.code === 0) {
          tableData.value = responseData.data.records.map(item => ({
            detailId: item.detailId,
            username: item.username,
            workLeaderName: item.workLeaderName,
            feeder: `${item.feeder.toFixed(0)}号线`, // 确保馈线是整数
            powerSupplyStation: item.powerSupplyStation,
            distributionRoomName: item.distributionRoomName,
            substationName: item.substationName,
            inspectionTeam: item.inspectionTeam,
            deviceName: item.device.deviceName,
            deviceType: convertDeviceType(item.device.deviceType),
            createTime: item.createTime
          }));
          renderFrontPage(frontPageCurrent);
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

// 将设备类型索引转换为文字描述
function convertDeviceType(index) {
  return data.range.find(item => item.value === index)?.text || '其他';
}

// 根据当前前端页码展示数据
const renderFrontPage = (currentPage) => {
  const start = (currentPage - 1) * frontPageSize;
  const end = start + frontPageSize;
  const slicedData = tableData.value.slice(start, end);
  displayedTableData.value = slicedData;
};

function convertToUtcFormat(dateStr, timezoneOffsetHours = 8) {
    // 解析日期字符串
    var date = new Date(dateStr);
    
    // 创建一个新的Date对象，减去时区偏移量以获取UTC时间
    var utcDate = new Date(date.getTime() - (timezoneOffsetHours * 60 * 60 * 1000));
    
    // 格式化为YYYY-MM-DDTHH:mm:ss.sssZ
    var year = utcDate.getUTCFullYear();
    var month = ("0" + (utcDate.getUTCMonth() + 1)).slice(-2); // Months are zero based
    var day = ("0" + utcDate.getUTCDate()).slice(-2);
    var hours = ("0" + utcDate.getUTCHours()).slice(-2);
    var minutes = ("0" + utcDate.getUTCMinutes()).slice(-2);
    var seconds = ("0" + utcDate.getUTCSeconds()).slice(-2);
    var milliseconds = ("00" + utcDate.getUTCMilliseconds()).slice(-3);

    return `${year}-${month}-${day}T${hours}:${minutes}:${seconds}.${milliseconds}Z`;
}

// 处理日期选择确认事件
const maskClick= (e) => {
	var startTime = convertToUtcFormat(queryValues.dateRange[0]);
	var endTime = convertToUtcFormat(queryValues.dateRange[1]);

};

// 组件挂载完成时加载数据
onMounted(() => {
  search();
});
</script>

<style lang="scss" scoped>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

.uni-container {
  width: 100%;
  height: 100%;
}

.item {
  -webkit-user-select: text;
  -moz-user-select: text;
  -ms-user-select: text;
  user-select: text;
}

.button-box {
  margin-top: 20px;
  margin-bottom: 20px;
  display: flex;
  justify-content: flex-start;
  gap: 30px;
  width: 300px;

  .button,
  .button-del {
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

.uni-pagination-box {
  margin-top: 25px;
}
</style>