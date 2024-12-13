<!-- item-left 菜单项左侧图标的固定样式类，用来根据层级单独控制样式
item-right 菜单右侧图标的固定样式类，用来根据层级单独控制样
bch-sub-node-box 子菜单容器，点击父菜单项，可展开/隐藏所有子项
controlItemRightIcon 父菜单右边的图标，数组，包含子项展开时图标和折叠时图标
item.rightIconState 当前被点击项的右侧菜单状态切换 -->

<template>
	<template :title="item.name" v-for="item in menuData">
		<view v-if="item.children.length" :class="[item.levelStyle]" :key="item.menu_id" @click.stop="onClickItem(item)">

			<!-- 菜单左侧 -->
			<view class="item-left">
				<!-- 控制缩进 -->
				<view class="bch-indent-filling-box" v-if="IsSpread">
					<view :class="['bch-indent-filling-item','bch-tabs-'+item.level+'-'+i++]" v-for="i in item.level" />
				</view>

				<!-- 左侧图标 -->
				<view :class="['bch-left-icon',item.icon]">
					<!-- 收缩状态下，图标左上角显示的小图标 -->
					<text :class="item.show?'iconfont icon-shouqi':'iconfont icon-zhankai'" v-show="!IsSpread"></text>
				</view>
				<!-- 文字 -->
				<view>{{item.name}}</view>
			</view>

			<!-- 父菜单右侧的图标（展开/折叠） -->
			<view :class="['item-right',item.show?controlItemRightIcon[1]:controlItemRightIcon[0]]" v-if="IsSpread">
			</view>
		</view>

		<!-- 如果不含有下级菜单 -->
		<view v-else="item.children.length" :class="item.levelStyle" @click.stop="onClickItem(item)" :key="item.menu_id">
			<view class="item-left">
				<!-- 控制缩进 -->
				<view class="bch-indent-filling-box" v-if="IsSpread">
					<view :class="['bch-indent-filling-item','bch-tabs-'+item.level+'-'+i++]" v-for="i in item.level" />
				</view>

				<!-- 左侧图标 -->
				<view :class="['bch-left-icon',item.icon]" />
				<!-- 文字 -->
				<view>{{item.name}}</view>
			</view>
		</view>

		<!-- 动画，需要下载安装uni-transition -->
		<!-- <uni-transition mode-class="fade" :show="item.show">
			<view class="bch-sub-node-box" v-if="item.children.length">
				<recursive-menu :menu="item.children" :controlItemRightIcon="controlItemRightIcon" :IsSpread="IsSpread"
					:onClick="onClick" />
			</view>
		</uni-transition> -->

		<!-- 无动画 -->
		<view class="bch-sub-node-box" v-if="item.children.length" v-show="item.show">
			<recursive-menu :menu="item.children" :controlItemRightIcon="controlItemRightIcon" :IsSpread="IsSpread"
				:onClick="onClick" />
		</view>
	</template>

</template>

<script>
	export default {
		name: "recursive-menu",
		data() {
			return {
				menuData: this.menu
			};
		},
		props: {
			menu: {
				type: Array
			},

			//父菜单右边的图标
			controlItemRightIcon: {
				type: Array,
				default: ['iconfont icon-shouqi', 'iconfont icon-zhankai']
			},

			//回调函数，向调用者提供当前被点击项的数据
			onClick: {
				type: Function,
				default: function(e) {
					console.log('这是默认回调函数，你需要在使用组件时，传入一个回调函数来拿到您当前点击的菜单项相关数据', e);

				}
			},
			IsSpread: {
				type: Boolean
			}

		},
		methods: {
			onClickItem(e) {

				e.show = !e.show
				// e.rightIconState = !e.rightIconState

				//把当前菜单的数据传回
				this.onClick(e)


				// if (e.show) {
				// 	this.$refs[`abc${e.id}`][0].$el.style.display = 'block'
				// 	setTimeout(() => {
				// 		this.$refs[`abc${e.id}`][0].$el.style.opacity = '1'
				// 	}, 200)
				// } else {

				// 	this.$refs[`abc${e.id}`][0].$el.style.transition = 'opacity 1s'
				// 	this.$refs[`abc${e.id}`][0].$el.style.opacity = '0.1'
				// 	setTimeout(() => {
				// 		this.$refs[`abc${e.id}`][0].$el.style.display = 'none'

				// 	}, 200)
				// }
				// console.log(this.$refs[`abc${e.id}`].style.fontSize='30px');

				// this.$refs[`abc${e.id}`][0].$el.style.transition='all 5s ease 0s'
				// this.$refs[`abc${e.id}`][0].$el.style.display='none'
				// console.log(this.$refs[`abc${e.id}`][0].$el.style);
			}
		}
	}
</script>

<style lang="scss">
	@import '../../static/bch-tree-menu.scss';
	@import '../../static/iconfont.css';
</style>