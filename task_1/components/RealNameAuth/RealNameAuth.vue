<template>
	<view class="backgroundBox">
		<view class="boxTop">
			<view class="boxtitle">
				<view>企业实名认证</view>
				<view style="color: #165dff;">修改认证主体</view>
			</view>
			<view class="boxcontent">
				<view class="content">
					<view class="box">
						<view style="color: #86909c;">账号类型 :</view>
						<view>企业账号</view>
					</view>
					<view class="box">
						<view style="color: #86909c;">认证状态 :</view>
						<view>已认证</view>						
					</view>
					<view class="box">
						<view style="color: #86909c;">认证时间 :</view>
						<view>2018-10-22 14:53:12</view>						
					</view>
					<view class="box">
						<view style="color: #86909c;">法人姓名 :</view>
						<view>李**</view>						
					</view>
					<view class="box">
						<view style="color: #86909c;">法人证件类型 :</view>
						<view>中国身份证</view>						
					</view>
					<view class="box">
						<view style="color: #86909c;">法人认证号码 :</view>
						<view>130************123</view>						
					</view>
					<view class="box">
						<view style="color: #86909c;">企业名称 :</view>
						<view>南方电网</view>						
					</view>
					<view class="box">
						<view style="color: #86909c;">企业证件类型 :</view>
						<view>企业营业执照</view>						
					</view>
					<view class="box">
						<view style="color: #86909c;">组织机构代码 :</view>
						<view>7*******9</view>						
					</view>
					
				</view>
			</view>
		</view>
		<view class="boxBottom">
			<view class="boxtitle">
				<view>认证记录</view>
			</view>
			<view class="boxcontent">
				<view class="content">
					<uni-table border stripe emptyText="暂无更多数据" >
						<!-- 表头行 -->
						<uni-tr>
							<uni-th align="center">认证类型</uni-th>
							<uni-th align="center">认证内容</uni-th>
							<uni-th align="center">当前状态</uni-th>
							<uni-th align="center">创建时间</uni-th>
							<uni-th align="center">操作</uni-th>
						</uni-tr>
						<!-- 表格数据行 -->
						<uni-tr>
							<uni-td align="center">企业证件认证</uni-td>
							<uni-td align="center">企业实名认证，法人姓名：李**</uni-td>
							<uni-td align="center">审核中</uni-td>
							<uni-td align="center">2021-02-28 10:30:50</uni-td>
							<uni-td style="display: flex;"  align="center">
								<view style="margin-left: 20px;color: #165dff;">查看</view>
								<view style="margin-left: 20px;color: #165dff;">撤回</view>
							</uni-td>
							
						</uni-tr>
						<uni-tr>
							<uni-td align="center">企业证件认证</uni-td>
							<uni-td align="center">企业实名认证，法人姓名：李**</uni-td>
							<uni-td align="center">审核中</uni-td>
							<uni-td align="center">2021-02-28 10:30:50</uni-td>
							<uni-td style="display: flex;"  align="center">
								<view style="margin-left: 20px;color: #165dff;">查看</view>
								<view style="margin-left: 20px;color: #165dff;">撤回</view>
							</uni-td>
						</uni-tr>
					</uni-table>
				</view>
			</view>
		</view>
	</view>
</template>

<script setup>

</script>

<style lang="scss" scoped>
.backgroundBox{
	width: 100%;
	padding: 20px;
	height: 100%;
	
	
	.boxTop{
		width: 100%;
		height: 40%;
		.boxtitle{
			padding: 5px;
			font-size: 18px;
			height: 20%;
			display: flex;
			justify-content: space-between; /* 这个属性会使子元素分布在容器的两端 */

		}
		.boxcontent{
			height: 55%;
			padding: 5px;
			justify-content: center;
			align-items: center;
			background-color:#f7f8fa ;
			overflow: hidden; /* 初始状态下不显示滚动条 */

			.content{
				width: 100%;
				padding: 10px;
				font-size: 14px;
				display: grid;
			    gap: 10px; /* 子元素之间的间距 */
				grid-template-columns: 1fr 1fr 1fr; /* 创建两列，每列宽度相等 */
			    overflow-x: auto; /* 当内容超出容器宽度时，显示水平滚动条 */
			    white-space: nowrap; /* 防止内容换行 */	
				.box{
					display: flex;
					flex-wrap: nowrap; /* 防止子元素换行 */

				}
			}
		}
	}
	
	.boxBottom{
		width: 100%;
		height: 60%;
		.boxtitle{
			padding: 5px;
			font-size: 18px;
			height: 15%;
			display: flex;
			justify-content: space-between; /* 这个属性会使子元素分布在容器的两端 */
		
		}
		.boxcontent{
			height: 75%;
			padding: 5px;
			justify-content: center;
			align-items: center;
		}
	}
}
</style>