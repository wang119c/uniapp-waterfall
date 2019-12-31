<template>
	<view class="container">
		<!-- 瀑布流 开始 -->
		<view class="wrap-water-flow">
			<view class="water-flow-container">
				<!-- 分成左右两列 -->
				<view class="water-column" style="margin-right: 20rpx;">
					<view class="left" id="left">
						<!-- 这里放单个的item 开始-->
						<block v-for="(item, index) in leftData" :key="index">
							<view class="flow-box-item-warp">
								<view class="show-icon"><image src="../../static/icon_video.png"></image></view>
								<view class="pic"><image v-bind:src="item.cover.url" mode="widthFix"></image></view>
								<view class="content">
									<text class="content-text">{{ item.title }}</text>
								</view>
								<view class="user">
									<view class="user-left">
										<image v-bind:src="item.user_info.avatarUrl" mode="widthFix"></image>
										<text>{{ item.user_info.nickName }}</text>
									</view>
									<view class="user-right">
										<image src="../../static/icon_zan_active.png" mode="widthFix"></image>
										<text>{{ item.likes }}</text>
									</view>
								</view>
							</view>
						</block>
						<!-- 这里放单个的item 结束-->
					</view>
				</view>
				<view class="water-column">
					<view class="right" id="right">
						<!-- 这里放单个的item 开始-->
						<block v-for="(item, index) in rightData" :key="index">
							<view class="flow-box-item-warp">
								<view class="show-icon"><image src="../../static/icon_video.png"></image></view>
								<view class="pic"><image v-bind:src="item.cover.url" mode="widthFix"></image></view>
								<view class="content">
									<text class="content-text">{{ item.title }}</text>
								</view>
								<view class="user">
									<view class="user-left">
										<image v-bind:src="item.user_info.avatarUrl" mode="widthFix"></image>
										<text>{{ item.user_info.nickName }}</text>
									</view>
									<view class="user-right">
										<image src="../../static/icon_zan_active.png" mode="widthFix"></image>
										<text>{{ item.likes }}</text>
									</view>
								</view>
							</view>
						</block>
						<!-- 这里放单个的item 结束-->
					</view>
				</view>
			</view>
		</view>
		<!-- 瀑布流 结束 -->
	</view>
</template>

<script>
const jsonData = require('@/common/data.json');
export default {
	data() {
		return {
			loading: false,
			data: [],
			leftData: [],
			rightData: []
		};
	},
	onLoad() {},
	mounted() {
		this.getList(true);
	},
	onReachBottom() {
		this.getList(false);
	},
	methods: {
		getList(refresh) {
			//这里是模拟的数据
			this.data = jsonData.data;
			//增加 refresh 为了 扩展 选项卡 ,可以初始化数据源
			if (refresh) {
				this.leftData = [];
				this.rightData = [];
			}
			//处理节点信息
			const query = uni.createSelectorQuery().in(this);
			this.columnNodes = query.selectAll('#left, #right');
			new Promise(resolve => {
				this.render(this.data, 0, refresh, () => {
					resolve();
				});
			});
		},
		render(data, i, refresh, success) {
			if ((data.length > i || refresh) && this.data.length !== 0) {
				this.columnNodes.boundingClientRect().exec(res => {
					const rects = res[0];
					this.leftHeight = rects[0].height;
					this.rightHeight = rects[1].height;
					if (this.leftHeight <= this.rightHeight || refresh) {
						this.leftData.push(data[i]);
					} else {
						this.rightData.push(data[i]);
					}
					//这里主要是等待再次渲染 不能获取正确的数据
					var _this = this;
					setTimeout(function() {
						_this.render(data, ++i, false, success);
					}, 50);
				});
			} else {
				success && success();
			}
		}
	}
};
</script>

<style>
.container {
	background: #f2f2f2;
	overflow: hidden;
}
.wrap-water-flow {
	padding: 20rpx;
}
.water-flow-container {
	display: flex;
	width: 100%;
	box-sizing: border-box;
}
.water-column {
	flex: 1;
}
.flow-box-item-warp {
	position: relative;
	width: 100%;
	height: 100%;
	border-radius: 10rpx;
	overflow: hidden;
	box-sizing: border-box;
	background: #ffffff;
	margin-bottom: 10rpx;
}
.flow-box-item-warp .show-icon {
	position: absolute;
	top: 10rpx;
	right: 10rpx;
	z-index: 9999;
}
.flow-box-item-warp .show-icon > image {
	width: 42rpx;
	height: 42rpx;
}
.flow-box-item-warp .pic {
	background: #f6f6f6;
}
.flow-box-item-warp .pic > image {
	width: 100%;
	display: block;
}
.flow-box-item-warp .content {
	overflow: hidden;
	padding: 20rpx 10rpx;
	box-sizing: border-box;
}
.flow-box-item-warp .content .content-text {
	color: #333;
	width: 100%;
	font-size: 28rpx;
	margin-bottom: 20rpx;
	font-weight: 500;
	overflow: hidden;
	text-overflow: ellipsis;
	display: -webkit-box;
	-webkit-box-orient: vertical;
	-webkit-line-clamp: 2;
	line-height: 33rpx;
}
.flow-box-item-warp .user {
	display: flex;
	overflow: hidden;
	justify-content: space-between;
	padding-right: 10rpx;
	padding-left: 10rpx;
	margin-bottom: 10rpx;
}
.flow-box-item-warp .user-left {
	flex: 1;
	display: flex;
}
.flow-box-item-warp .user-left > image {
	width: 32rpx;
	height: 32rpx;
	border-radius: 50%;
}
.flow-box-item-warp .user-left > text {
	font-size: 22rpx;
	line-height: 32rpx;
	margin-left: 10rpx;
}
.flow-box-item-warp .user-right {
	overflow: hidden;
}
.flow-box-item-warp .user-right > image {
	width: 28rpx;
	height: 28rpx;
	float: left;
	margin-right: 10rpx;
}
.flow-box-item-warp .user-right > text {
	font-size: 22rpx;
	line-height: 28rpx;
	color: rgba(51, 51, 51, 1);
	float: right;
}
</style>
