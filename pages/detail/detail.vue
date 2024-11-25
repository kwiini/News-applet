<template>
	<view class="detail">
		<view class="title">{{ detail.title }}</view>
		<view class="info">
			<view class="author">编辑：{{ detail.author }}</view>
			<view class="time">发布日期：{{ detail.posttime }}</view>
		</view>
		<view class="content">
			<rich-text :nodes="detail.content"></rich-text>
		</view>
		<view class="description">
			声明：侵权删侵权删侵权删侵权删侵权删侵权删侵权删侵权删侵权删侵权删侵权删侵权删侵权删侵权删侵权删侵权删侵权删侵权删侵权删侵权删侵权删侵权删
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				options: null,
				detail: {}
			};
		},
		onLoad(e) {
			this.options = e;
			this.getDetial();
		},
		methods: {
			getDetial() {
				uni.request({
					url: "https://ku.qingnian8.com/dataApi/news/detail.php",
					data: this.options,
					success: res => {
						console.log(res);
						res.data.posttime = this.timestampToTime(res.data.posttime);
						res.data.content = res.data.content.replace(/<img/gi, '<img style="max-width: 100%"');
						this.detail = res.data;
						this.saveHistory();
						uni.setNavigationBarTitle({
							title: this.detail.title
						})
					}
				})
			},
			/* 时间戳转换为时间 */
			timestampToTime(timestamp) {
			    timestamp = timestamp ? timestamp : null;
			    let date = new Date(timestamp * 1000);	//时间戳为10位需*1000，时间戳为13位的话不需乘1000，此处为10位
			    let Y = date.getFullYear() + '-';
			    let M = (date.getMonth() + 1 < 10 ? '0' + (date.getMonth() + 1) : date.getMonth() + 1) + '-';
			    let D = (date.getDate() < 10 ? '0' + date.getDate() : date.getDate()) + ' ';
			    let h = (date.getHours() < 10 ? '0' + date.getHours() : date.getHours()) + ':';
			    let m = (date.getMinutes() < 10 ? '0' + date.getMinutes() : date.getMinutes()) + ':';
			    let s = date.getSeconds() < 10 ? '0' + date.getSeconds() : date.getSeconds();
			    return Y + M + D + h + m + s;
			},
			// 存储历史记录
			saveHistory() {
				let historyArr = uni.getStorageSync("historyArr") || [];
				let item = {
					id: this.detail.id,
					classid: this.detail.classid,
					picurl: this.detail.picurl,
					title: this.detail.title,
					looktime: this.timestampToTime(Date.now())
				}
				let index = historyArr.findIndex(i => {
					return i.id == this.detail.id;
				})
				if (index >= 0) {
					historyArr.splice(index, 1);
				}
				historyArr.unshift(item);
				historyArr = historyArr.slice(0, 10);
				uni.setStorageSync("historyArr", historyArr);
			}
		}
	}
</script>

<style lang="scss">
	.detail {
		padding: 30rpx;
		.title {
			font-size: 46rpx;
			color: #333;
		}
		.info {
			background-color: #f6f6f6;
			padding: 20rpx;
			font-size: 25rpx;
			color: #666;
			display: flex;
			justify-content: space-between;
			margin: 30rpx 0;
		}
		.content {
			padding-bottom: 50rpx;
		}
		.description {
			background-color: #fef0f0;
			font-size: 26rpx;
			padding: 20rpx;
			color: #F89898;
			line-height: 1.8rem;
		}
	}
</style>
