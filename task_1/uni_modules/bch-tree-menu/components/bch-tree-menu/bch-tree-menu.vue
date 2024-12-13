<!-- 样式类名：
spreadStyle 面板展开操作时的过渡动画样式
NoSpreadStyle 面板折叠操作进时的过渡动画样式
control-button 面板控制按钮(可能通过CSS控制其部分样式)
H5-menu-l-box-spread 面板展开时，所有子项的样式
H5-menu-l-box-NoSpread 面板折叠时，所有子项的样式

内部数据：
IsSpread 宽窄面板切换，true:展开，false：收缩，只显示图标

props:
controlButtonIocn 面板控制按钮的图标（类名），默认值：['iconfont icon-jiantou_xiangzuoliangci','iconfont icon-jiantou_xiangyouliangci']
controlItemRightIcon 父级菜单项右侧图标
menuData 菜单的JSON数据 -->

<template>
	<view :class="IsSpread?'H5-menu-l-box-spread':'H5-menu-l-box-NoSpread'" :style="IsSpread?spreadStyle:NoSpreadStyle">
		<view class="control-button" @tap="control_button_tap">
			<view :class="controlButtonIocn[0]" v-show="IsSpread" />
			<view :class="controlButtonIocn[1]" v-show="!IsSpread" />
		</view>

		<scroll-view class="menu-box" scroll-y="true" show-scrollbar="true">
			<!-- 递归渲染菜单项 -->
			<recursive-menu :menu="treeData" :controlItemRightIcon="controlItemRightIcon" :IsSpread="IsSpread"
				:onClick="onClick" />
		</scroll-view>

	</view>
</template>
<script>
	export default {
		name: "bch-tree-menu", 
		data() {
			return {
				IsSpread: true
			};
		},
		props: {

			// 控制面板展开/收缩的按钮图标，类名数组
			controlButtonIocn: {
				type: Array,
				default: ['iconfont icon-jiantou_xiangzuoliangci', 'iconfont icon-jiantou_xiangyouliangci']
			},

			//带有子项的菜单项右侧图标类名，包含子项展开时图标和折叠时图标
			controlItemRightIcon: {
				type: Array
			},

			//菜单展开时的样式
			spreadStyle: {
				type: Object,
				default: {
					'width': '200px',
					'transition': '0.4s'
				}
			},

			//菜单收缩时的样式
			NoSpreadStyle: {
				type: Object,
				default: {
					'width': '70px',
					'transition': '0.4s'
				}
			},

			//回调函数，返回当前项的数据
			onClick: {
				type: Function
			},

			// 默认菜单项的JSON数据
			menuData: {
				type: Array,
				default: [{
						menu_id: 1,
						parent_id: null,
						show: false,
						name: '示例菜单',
						icon: 'iconfont icon-leibie',
					},
					{
						menu_id: 2,
						parent_id: 1,
						show: false,
						name: '示例菜单1',
						icon: 'iconfont icon-gongsi',
					}
				]
			}

		},

		methods: {
			control_button_tap() {
				this.IsSpread = !this.IsSpread
			},

		},
		computed: {
			//计算属性，把数据整理成树状结构
			treeData() {

				// console.log('>>>>>>>>>>>>>>>>>');
				// 处理成带children的数据###########################################################################
				// 处理成带children的数据###########################################################################
				// 处理成带children的数据###########################################################################
				// 处理成带children的数据###########################################################################

				// 定义一个变量,接收处理结果
				let treeData = []
				handleTree(this.menuData)
				//处理函数
				function handleTree(arr) {
					let ss = 0

					//如果parent_id取反为true，则没有上级id,自动作为根节点
					let root = arr.filter(i => !i.parent_id)

					//更新：如果存在parent_id为true的，则说明有下级	
					if (arr.filter(i => !!i.parent_id)) {

						//以下####更新为多个根节点
						root.forEach(v => {
							//重置ss的值
							ss = 0
							// 最后接收结果
							let obj = recursive(arr, v.menu_id, v)
							treeData.push(obj)
						})

					}

					//递归函数
					function recursive(arr, fid, fatheritem) {
						//当前项目的子项目勾选的数量
						fatheritem.childrenSelectedCount = 0
						//当前项目是否勾选
						fatheritem.selected = false
						//当前项目的子项是否显示
						fatheritem.show = false

						//项目层级，顶层为0
						fatheritem.level = ++ss
						//项目层级样式类，方便控制每一层的样式
						fatheritem.levelStyle = `bch-node-level-style-${ss}`

						//当前项目的子集
						fatheritem.children = []
						//当前项目的儿子ids
						fatheritem.childrenIds = []
						//当前项目的儿子titles
						fatheritem.childrenTitles = []

						//这个arr,就是this.viewData
						arr.forEach(x => {
							if (x.parent_id == fid) {

								//如果当前项目的parent_id==fid,说明是fatheritemr的子项，就push进去
								fatheritem.children.push(x)
								fatheritem.childrenIds.push(x.menu_id)
								fatheritem.childrenTitles.push(x.title)


								//接着判断当前这个项目下有没有子项
								if (arr.filter(i => i.parent_id == x.menu_id)) {
									// 说明有子项
									recursive(arr, x.menu_id, x)
								}
								ss--
							}

						})

						// 返回结果
						return fatheritem

					}
				}
				// console.log('处理后的数据====》》》》》', treeData);
				// 返回处理后的数据
				return treeData
			}

		}
	}
</script>

<style lang="scss">
	@import '../../static/bch-tree-menu.scss';
	@import '../../static/iconfont.css';
</style>