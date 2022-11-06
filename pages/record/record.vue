<template>
	<view class="content">
		<view class="uni-comment" v-for="(item,value) in list">
			<view class="uni-comment-list u-border-bottom">
				<view class="uni-comment-face">
					<u-avatar :src="item.handpic" mode="circle" size="70"></u-avatar>
				</view>
				<view class="uni-comment-body">
					<view class="uni-comment-top">
						<text>{{item.name}}</text>
					</view>
					<!-- 输出留言 -->
					<view class="uni-comment-content">
						<text>{{item.context}}</text>
						<view class="messagepic" v-if="item.tuPiansrc">
							<image :src="item.tuPiansrc" mode="widthFix" lazy-load  @click="clickImg(item)">
						</view>
					</view>
					<view class="uni-comment-date">
						<text>{{item.publishTime}}</text>
					</view>
				</view>
			</view>

		</view>
	</view>
</template>

<script>
	export default {

		data() {
			return {
				list: []
			}
		},
		methods: {
			//调用云函数,从数据库中获取所有留言
			getMessage() {
				uniCloud.callFunction({
					//调用函数名
					name: 'fun',
					data: {
						api: "getMessages"
					}
				}).then(res => {
					//将获取的留言存入list集合中,集合中的每一个元素的属性有:_id,context,tupian.jpg
					this.list = res.result.data.reverse()
				}) //getMessage
			},
			clickImg(item){
			  // 预览图片
			  uni.previewImage({
			    urls: [item.tuPiansrc],
			    current: '', // 当前显示图片的http链接，默认是第一个
			  });
			}
		}, //methods

		onLoad() {
			this.getMessage()
		},
		// 监听下拉刷新
		onPullDownRefresh() {
			setTimeout(() => {
				this.getMessage();
				uni.stopPullDownRefresh(); //关闭刷新状态
			}, 1000);
		}
	}
</script>

<style>
	.content {
		/* background: -webkit-linear-gradient(top, #E5EED3, white); */
		padding: 0rpx;
		margin: 0rpx;
	}

	.uni-comment {
		padding: 24rpx 24rpx;
		display: flex;
		flex-grow: 1;
		flex-direction: column;
	}

	.uni-comment-list {
		flex-wrap: nowrap;
		padding: 10rpx 0;
		margin: 10rpx 0;
		width: 100%;
		display: flex;
	}

	.uni-comment-face {
		margin-right: 20upx;
	}

	.uni-comment-body {
		width: 100%;
	}

	.uni-comment-top {
		line-height: 1.5em;
		justify-content: space-between;
	}

	.uni-comment-top text {
		color: #FBDA95;
		font-size: 24upx;
	}

	.uni-comment-content {
		line-height: 1.6em;
		font-size: 28upx;
		padding: 8rpx 0;
	}
	.messagepic {
		width: 200rpx;
		height: 200rpx;
	}
	.messagepic image{
		width: 100%;
		height: 100%;
	}
	
	

	.uni-comment-date {
		line-height: 38upx;
		flex-direction: row;
		justify-content: space-between;
		display: flex !important;
		flex-grow: 1;
	}

	.uni-comment-date view {
		color: #666666;
		font-size: 24upx;
		line-height: 38upx;
	}
</style>
