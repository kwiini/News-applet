<template>
	<view class="user">
		<view class="top">
			<image src="../../static/images/R-C.jpg" mode=""></image>
			<text>浏览历史</text>
		</view>
		<view class="content">
			<view class="row" v-for="item in listArr">
				<newbox :item="item" @click.native="goDetail(item)"></newbox>
			</view>
		</view>
		<view class="nohistory" v-if="!listArr.length">
			<image src="../../static/images/R-C.jpg" mode="widthFix"></image>
			<view class="text">暂无浏览记录</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				listArr: []
			};
		},
		onShow() {
			this.getData();
		},
		methods: {
			// 跳转到详情页面
			goDetail(item) {
				uni.navigateTo({
					url: `/pages/detail/detail?cid=${item.classid}&id=${item.id}`
				})
			},
			// 获取缓存浏览记录
			getData() {
				let hisArr = uni.getStorageSync("historyArr") || [];
				this.listArr = hisArr;
			}
		}
	}
</script>

<style lang="scss">
	.user {
		.top {
			padding: 50rpx 0;
			background-color: #f8f8f8;
			color: #666;
			display: flex;
			flex-direction: column;
			justify-content: center;
			align-items: center;
			image {
				width: 150rpx;
				height: 150rpx;
			}
			.text {
				font-size: 38rpx;
				padding-top: 20rpx;
			}
		}
	}
	.content {
		padding: 30rpx;
		.row {
			border-bottom: 1px dashed #efefef;
			padding: 20rpx 0;
		}
	}
	.nohistory {
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		image {
			width: 450rpx;
		}
		.text {
			font-size: 26rpx;
			color: #888;
		}
	}
</style>
