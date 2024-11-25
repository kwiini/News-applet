<template>
	<view class="home">
		<scroll-view scroll-x class="navScroll">
			<view class="item" 
				:class="index==navIndex ? 'active' : ''" 
				v-for="(item, index) in navArr" 
				@click="clickNav(index, item.id)"
				:key="item.id"
			>{{ item.classname }}</view>
		</scroll-view>
		
		<view class="content">
			<view class="row" v-for="item in newsArr" :key="item.id">
				<newbox :item="item" @click.native="goDetail(item)"></newbox>
			</view>
		</view>
		<view class="nodata" v-if="!newsArr.length">
			<image src="../../static/images/R-C.jpg" mode="widthFix"></image>
		</view>
		<view class="loading" v-if="newsArr.length">
			<view v-if="loading==1">数据加载中……</view>
			<view v-if="loading==2">没有更多了~</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				navIndex: 0,
				navArr: [],
				newsArr: [],
				currentPage: 1,
				loading: 0		// 0：默认；1：加载中；2：没有更多了
			}
		},
		onLoad() {
			this.getNavData();
			this.getNewsData();
		},
		onReachBottom() {
			// console.log("123");
			if (this.loading === 2) {
				return;
			}
			this.currentPage++;
			this.loading = 1;
			this.getNewsData();
		},
		methods: {
			// 点击导航切换样式
			clickNav(index, id) {
				this.navIndex = index;
				this.currentPage = 1;
				this.loading = 0;
				this.newsArr = [];
				this.getNewsData(id);
			},
			// 跳转到详情页面
			goDetail(item) {
				uni.navigateTo({
					url: `/pages/detail/detail?cid=${item.classid}&id=${item.id}`
				})
			},
			// 获取列表导航数据
			getNavData() {
				uni.request({
					url: "https://ku.qingnian8.com/dataApi/news/navlist.php",
					success: res => {
						console.log(typeof res.data);
						this.navArr = res.data;
					}
				})
			},
			// 获取新闻列表
			getNewsData(id = 50) {
				uni.request({
					url: "https://ku.qingnian8.com/dataApi/news/newslist.php",
					data: {
						cid: id,
						page: this.currentPage
					},
					success: res => {
						console.log(res);
						if (res.data.length === 0) {
							this.loading = 2;
						}
						this.newsArr = [...this.newsArr, ...res.data];
					}
				})
			}
		}
	}
</script>

<style lang="scss" scoped>
	.navScroll {
		height: 100rpx;
		background-color: #F7F8FA;
		white-space: nowrap;
		position: fixed;
		top: var(--window-top);
		z-index: 20;
		bottom: 0;
		// 去除 H5 端滚动条
		/deep/ ::-webkit-scrollbar {
			width: 4px !important;
			height: 1px !important;
			overflow: auto !important;
			background: transparent !important;
			-webkit-appearance: auto !important;
			display: block;
		}
		.item {
			font-size: 40rpx;
			display: inline-block;
			line-height: 100rpx;
			padding: 0 30rpx;
			color: #333;
			&.active {
				color: #31c27c;
			}
		}
	}
	.content {
		padding: 30rpx;
		padding-top: 130rpx;
		.row {
			border-bottom: 1px dashed #efefef;
			padding: 20rpx 0;
		}
	}
	.nodata {
		display: flex;
		justify-content: center;
		image {
			width: 360rpx;
		}
	}
	.loading {
		text-align: center;
		font-size: 26rpx;
		color: #888;
		line-height: 2em;
	}
</style>
