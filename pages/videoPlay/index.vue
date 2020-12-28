<template>
	<view class="video_play">
		<image :src="videoObj.img"></image>
		<!-- 工具栏 开始 -->
		<view class="video_tool">
			<view @click="handelMuted" :class="['iconfont',muted?'iconjingyin':'iconshengyin']"></view>
			<view class="iconfont iconzhuanfa">
				<button open-type="share" class="btn"></button>
			</view>
		</view>
		<!-- 工具栏 结束 -->

		<!-- 视频 开始 -->
		<view class="video_wrap">
			<video :muted="muted" :src="videoObj.video" objectFit="fill"></video>
		</view>
		<!-- 视频 结束 -->
		<!-- button -->
		<view class="download">
			<view class="download_btn" @click="handelDownload">下载视频</view>
		</view>
	</view>
</template>

<script>
	export default{
		data() {
			return {
				videoObj: {},
				//是否禁音
				muted:false
			}
		},
		onLoad() {
			console.log(getApp().globalData)
			this.videoObj = getApp().globalData.video;
		},
		methods:{
			handelMuted(){
				this.muted = !this.muted;
			},
			async handelDownload(){
				//1:远程视频下载到小程序内存
				//2：内存中视频下载本地
				await uni.showLoading({
					title:"下载中"
				})
				const {tempFilePath} = (await uni.downloadFile({url:this.videoObj.video}))[1];
				await uni.saveVideoToPhotosAlbum({
					filePath:tempFilePath
				})
				
				uni.hideLoading();
				
				await uni.showToast({
					title:"下载成功"
				})
			}
		}
	}
</script>

<style lang="scss" scoped>
	.video_play{
		position: relative;
		image{
			position: absolute;
			width: 100vh;
			height: 100vh;
			z-index: -1;
			filter: blur(20px);
		}
		.video_tool{
			height: 80rpx;
			display: flex;
			justify-content: flex-end;
			.iconfont{
				width: 80rpx;
				color: #FFFFFF;
				font-size: 50rpx;
				border-radius: 50%;
				background-color: rgba(0,0,0, .2);
				display: flex;
				justify-content: center;
				align-items: center;
				margin-right: 20rpx;
			}
			.iconzhuanfa{
				position: relative;
				.btn{
					position: absolute;
					width: 100%;
					height: 100%;
					opacity: 0;
				}
			}
		}
		.video_wrap{
			display: flex;
			justify-content: center;
			video{
				width: 360rpx;
				height: 600rpx;
			}
		}
		.download{
			display: flex;
			justify-content: center;
			margin-top: 30rpx;
			.download_btn{
				width: 360rpx;
				height: 80rpx;
				border-radius: 40rpx;
				display: flex;
				justify-content: center;
				align-items: center;
				color: #FFFFFF;
				border: 1rpx solid #FFFFFF;
				background-color: rgba(0,0,0, .6);
			}
		}
	}
</style>
