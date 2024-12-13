# bch-tree-meun
	一个H5端（不仅限于）适配的，支持JSON树形结构数据递归渲染的，可收缩或展开的导航菜单插件。
		默认CSS样式支持三级导航，每级导航项目的图标、标题文字均可实现自定义
### [查看示例demo](https://static-mp-1825b60a-4c73-45f1-a747-44b94addf5da.next.bspapp.com/)		

# 属性列表及说明
```vue
	controlButtonIocn		控制左侧面板展开/收缩的按钮图标类名数组
		默认值：['iconfont icon-shouqi', 'iconfont icon-zhankai']，第1个是收缩状态下的图标，第2个是展开状态下的图标
	menuData				菜单项的JSON数据
		默认数据样例：
			[{
				menu_id: 1,
				parent_id: null,
				show: false,
				name: '示例菜单',
				menuClass: 'm-father ',
				leftIcon: 'iconfont icon-leibie',
				rightIconState: true,
			},
			{
				menu_id: 2,
				parent_id: 1,
				show: false,
				name: '示例菜单1',
				menuClass: 'm-start-leaf',
				leftIcon: 'iconfont icon-gongsi',
				rightIconState: true,
			}]
	
	spreadStyle				菜单面板展开时的样式，默认：{'width': '200px','transition': '0.4s'}
	NoSpreadStyle			菜单页面收缩时的样式，默认： {'width': '65px','transition': '0.4s'}
	controlItemRightIcon 	父菜单右边的图标，数组，包含子项展开时图标和折叠时图标
	onClick 				菜单项点击时的回调函数，返回当前菜单项的数据
	 
```

# 示例代码


```vue
<template>
	<view class="bch-header">
		头部
	</view>
	<view class="bch-content-box">
		<bch-tree-menu :menuData="menuData" :controlItemRightIcon="['iconfont icon-xiangyou','iconfont icon-youxia']"
			:controlButtonIocn="controlButtonIocn" :onClick="callback">
		</bch-tree-menu>
		
	</view>
</template>

<script>
	export default {
		data() {
			return {

				//bch-tree-menu的相关数据
				controlButtonIocn: ['iconfont icon-jiantou_xiangzuoliangci', 'iconfont icon-jiantou_xiangyouliangci'],
				itemData: null,
				menuData: [{
								menu_id: 1,
								parent_id: null,
								show: false,
								name: '基础数据',
								menuClass: 'm-father ',
								leftIcon: 'iconfont icon-leibie',
								rightIconState: true,
							},
							{
								menu_id: 2,
								parent_id: 1,
								show: false,
								name: '菜单1.1',
								menuClass: 'm-start-leaf',
								leftIcon: 'iconfont icon-gongsi',
								rightIconState: true,
							},
							{
								menu_id: 5,
								parent_id: null,
								show: false,
								name: '系统管理',
								menuClass: 'm-father',
								leftIcon: 'iconfont icon-peizhi1 custom-left-icon-class',
								rightIconState: true,
							}, {
								menu_id: 6,
								parent_id: 5,
								show: false,
								name: '菜单2.1',
								menuClass: 'm-end-leaf',
								leftIcon: 'iconfont icon-WEBSITE',
								rightIconState: true,
							}, {
								menu_id: 31,
								parent_id: null,
								show: false,
								name: '人员信息',
								menuClass: 'm-father',
								leftIcon: 'iconfont icon-peizhi1 custom-left-icon-class',
								rightIconState: true,
							}, {
								menu_id: 32,
								parent_id: 31,
								show: false,
								name: '菜单3.1',
								menuClass: 'm-end-leaf',
								leftIcon: 'iconfont icon-WEBSITE',
								rightIconState: true,
							}, {
								menu_id: 33,
								parent_id: 32,
								show: false,
								name: '菜单3.1-1',
								menuClass: 'm-end-leaf',
								leftIcon: 'iconfont icon-WEBSITE',
								rightIconState: true,
							}, {
								menu_id: 33,
								parent_id: 32,
								show: false,
								name: '菜单3.1-2',
								menuClass: 'm-end-leaf',
								leftIcon: 'iconfont icon-WEBSITE',
								rightIconState: true,
							}]
				
			}
		},

		methods: {
			callback(e) {
				console.log('根据业务逻辑进行处理', e);

			}
		}
	}
</script>

<style lang="scss">
	page {
		width: 100%;
		height: 100%;
		padding: 0px;
		overflow: hidden;

	}

	.bch-header {
		height: 75px;
		background: linear-gradient(to right, #064dbb, #33ccff);
		color: white;
		display: flex;
		align-items: center;
		font-size: 24px;
		padding: 5px;
		box-sizing: border-box;
	}

	.bch-content-box {
		height: calc(100% - 75px);
		display: flex;

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
```



# 依赖项
	1、uni_modules\components\static目录下面的文件说明
		样式文件：bch-tree-menu.scss
		图标字体文件：iconfont.css,iconfont.ttf,iconfont.woff,iconfont.woff2
	2、本插件依赖 uni-transition 过渡动画插件，请自行下载
### [uni-transition下载地址](https://ext.dcloud.net.cn/plugin?name=uni-transition)

# 自定义样式的说明
	1、直接修改样式文件，实现自定义样式。
	2、直接覆盖图标文件，在使用插件时，传入图标类名，实现自定义图标。
	3、如果您想废弃插件本身的样式，需要执行以下步骤
		【1】移除uni_modules\components\bch-tree-menu.vue文件中的样式引用：
			
			<style lang="scss">
				// @import '../../static/bch-tree-menu.scss';  	//注释此行，废除插件本身的样式
				// @import '../../static/iconfont.css';			//注释此行，废除插件本身的样式
			</style>
		【2】移除uni_modules\components\recursive-menu.vue文件中的样式引用：
			
			<style lang="scss">
				// @import '../../static/bch-tree-menu.scss';  	//注释此行，废除插件本身的样式
				// @import '../../static/iconfont.css';			//注释此行，废除插件本身的样式
			</style>
		【3】复制uni_modules\components\static目录下的bch-tree-menu.scss文件中的所有内容，粘贴到您的项目uni.scss文件中根据情况进行自定义。
		【4】根据第三方图标库的使用教程，通过传入图标类名，实现自定义图标。
		
		注意：
			（1）导航菜单项左侧的图标类名通过menuData中的leftIcon属性传入
			（2）导航菜单项右侧的图标类名通过插件的controlItemRightIcon属性传入。
			（3）折叠面板的控制按钮图标类名通过插件的controlButtonIocn属性传入。



	
	