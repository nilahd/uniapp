<!-- menu_id: 24 -->
<template>
  <view>
    <view class="query-container">
      <view class="query-item">
        <text class="text-box">业务ID :</text>
        <input class="input-box" v-model="queryValues.detailId" placeholder="请输入业务ID" />
        <text class="text-box">账户名称 :</text>
        <input class="input-box" v-model="queryValues.username" placeholder="请输入账户名称" />
        <text class="text-box">设备类型 :</text>
        <uni-data-select class="input-box" v-model="data.value" :localdata="data.range" @change="select" />
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
    <view class="button-box">
	  <button class="button" @click="selectALL" type="primary">全选</button>
      <button class="button" @click="search" type="primary">查询</button>
      <button class="button" @click="reset" type="primary">重置</button>
      <button class="button" @click="download" type="primary">下载</button>
<!--      <button class="button-del" @click="del" type="warn">删除</button>	 --> 
    </view>
    <view class="jpg-box">
      <view class="box" v-for="(item, index) in displayedTableData" :key="index">
        <view class="img">
          <view class="select-circle" :class="{ 'selected': item.isSelected }" @click="toggleSelect(item)"></view>

          <image @click="previewImage(item.previewUrl)" :src="item.previewUrl" mode="aspectFit"></image>
          <view class="img-text-box">
            <text class="img-text">这是图片描述</text>
            <text class="img-text">这是图片描述</text>
          </view>
        </view>
      </view>
    </view>
    <view class="uni-pagination-box">
      <uni-pagination show-icon :page-size="frontPageSize" :current="frontPageCurrent"
                      :total="tableData.length" @change="change" />
    </view>
  </view>
</template>

<script setup>
import { onMounted, ref, reactive, watch } from 'vue';
import { saveAs } from 'file-saver';
import JSZip from 'jszip';
const data = reactive({
  value: null,
  range: [
    { value: 0, text: "开关柜" },
    { value: 1, text: "变压器" },
    { value: 2, text: "电缆" },
    { value: 3, text: "其他" }
]});
const previewImage = (src) => {
  uni.previewImage({
    urls: [src]
  });
};
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
const isAllSelected = ref(false); // 添加此变量用于标识是否全选状态
const tableData = ref([]);
const displayedTableData = ref([]);
const pageSize = ref(50);
const frontPageSize = ref(10);
let frontPageCurrent = ref(1);
const total = ref(0);
const loading = ref(false);
const selectedIds = reactive([]); // 用作跟踪所有被选中的项目 ID
const selectedImages = reactive({});

const toggleSelect = (item) => {
  item.isSelected = !item.isSelected; // 直接切换选中状态
  
  if (item.isSelected) {
    // 添加选中的图片的 ID 到列表中
    selectedIds.push(item.imageId);
  } else {
    // 删除不再选中的条目的 ID
    const index = selectedIds.indexOf(item.imageId);
    if (index !== -1) {
      selectedIds.splice(index, 1);
    }
  }
};


const selectALL = () => {
  isAllSelected.value = !isAllSelected.value;
  displayedTableData.value.forEach(item => {
    item.isSelected = isAllSelected.value;
    if (isAllSelected.value) {
      // 添加选中的图片到对象中
      selectedImages[item.imageId] = item.previewUrl;
    } else {
      // 移除不选中的图片的对象中的记录
      delete selectedImages[item.imageId];
    }
  });
  console.log(selectedImages);
};
  
const del = () => {
  console.log('Image Ids:', selectedImages);
  
};

const download = async () => {
  try {
    const zip = new JSZip();

    // 遍历所有选定的图片数据
    for (const [imageId, imageUrl] of Object.entries(selectedImages)) {
      // 获取图片
      const response = await fetch(imageUrl);
      const blob = await response.blob();

      // 使用图片的 imageId 命名
      const fileName = `${imageId}.jpg`;

      // 将 Blob 对象添加到 ZIP 文件中
      zip.file(fileName, blob, { binary: true });
    }

    // 生成并下载 ZIP 文件
    const content = await zip.generateAsync({ type: 'blob' });
    saveAs(content, 'images.zip');
	Object.keys(selectedImages).forEach(key => delete selectedImages[key]);

  } catch (error) {
    console.error('Download error:', error);
  }

  reset();
};

const change = (e) => {
  frontPageCurrent.value = e.current;
  renderFrontPage(frontPageCurrent.value);
  // 清除全选状态并清空 selectedIds
  isAllSelected.value = false;
};

const search = () => {
  frontPageCurrent.value = 1;
  // console.log(data.value);
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
  renderFrontPage(frontPageCurrent.value);
};

const reset = () => {
  Object.keys(queryValues).forEach(key => {
    queryValues[key] = '';
  });
  data.value = null;
  frontPageCurrent.value = 1;
  selectedImages.value = [];

  search();
};

const getData = (currentPage = 1, value = '') => {
  loading.value = true;
  uni.request({
    url: 'http://43.139.118.127/api/detail/pageVo',
    method: 'POST',
    header: {
      'content-type': 'application/json'
    },
    data: value,
    success: (res) => {
      if (res.statusCode === 200) {
        const responseData = res.data;
        if (responseData.code === 0) {
          tableData.value = responseData.data.records.map(item => ({
            id: item.id,
            isSelected: false,
            imageId: item.image.imageId,
            previewUrl: item.previewUrl || '',
			
          }));
          renderFrontPage(frontPageCurrent.value);
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
const renderFrontPage = (currentPage) => {
  const start = (currentPage - 1) * frontPageSize.value;
  const end = start + frontPageSize.value;
  displayedTableData.value = tableData.value.slice(start, end);
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
  const startTime = convertToUtcFormat(queryValues.dateRange[0]);
  const endTime = convertToUtcFormat(queryValues.dateRange[1]);
};
onMounted(() => {
  search();
});
</script>


<style lang="scss" scoped>
/* #ifndef H5 */
/* page {
	padding-top: 85px;
} */
/* #endif */
* {
	margin: 0;
	padding: 0;
	box-sizing: border-box;
}
.uni-pagination-box{
	margin-top: 20px;
}
.jpg-box{
	width: 100%;
	height: 100%;
	display: grid;
	grid-template-columns: repeat(5,1fr);
	grid-gap: 1px;
	.box{
		.select-circle {
			width: 15px;
			height: 15px;
			border-radius: 50%;
			border: 1px solid #b8b8b8;
			margin-left: 5px;
			margin-top: 5px;
			&.selected {
				background-color: #0fc7ff;
			}
		}
		margin-top: 5px;
		width: 275px;
		border: black solid 1px;
		height: 240px;
		justify-content: center;
		align-items: center;
		border-radius: 5px;

		.img{
			width: 100%;
			height: 100%;
			background-color: #c3c3c3;
			position: relative;
			display: inline-block;
			border-radius: 5px;
			
			.img-text-box{
				    position: absolute;
				    bottom: 0; 
				    left: 0;
				    width: 100%;
				.img-text {
					  color: white;
					  font-size: 14px;
					  display: block;
					  line-height: 1.5;
					  margin-left: 5px;
					  margin-bottom: 2px;
				}
			}
			image{
				width: 268.8px;
				height: 201.6px;
				justify-content: center;
				align-items: center;
			}
		}

	}
}


.button-box{
	margin-top: 15px;
	margin-bottom: 10px;
	display: flex;
	justify-content: flex-start;
	gap: 30px;  
	width: 400px;
	.button{
		width: 80px;
		height: 30px;
		font-size: 16px;
		display: flex;
		align-items: center;  
		justify-content: center;
		flex-grow: 1;
		flex-shrink: 1;
	}
	.button-del{
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

</style>
