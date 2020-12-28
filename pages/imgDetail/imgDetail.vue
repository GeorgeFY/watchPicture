<template>
	<view>
		<!-- 用户信息 开始 -->
		<view class="user_info">
			<view class="user_icon">
				<image :src="imgDetail.user.avatar" ></image>
			</view>
			<view class="user_desc">
				<view class="user_name">{{imgDetail.user.name}}</view>
				<view class="user_time">{{imgDetail.cnTime}}</view>
			</view>
		</view>
		<!-- 用户信息 结束 -->
		<!-- 大图 开始 -->
		<view class="high_img">
			<swiper-action @swiperAction="handelSwiperAction">
				<image :src="imgDetail.newThumb"></image>
			</swiper-action>
			
		</view>
		<!-- 大图结束 -->
		<!-- 点赞 开始 -->
		<view class="user_rank">
			<view class="rank">
				<text class="iconfont icondianzan">{{imgDetail.rank}}</text>
			</view>
			<view class="user_collect">
				<text class="iconfont iconshoucang">收藏</text>
			</view>
		</view>
		<!-- 点赞 结束 -->
		
		<!-- 专辑信息 开始 -->
		<view class="album-wrap" v-if="album.length">
			<view class="album-title"></view>
			<view class="album-list">
				<view class="alnum-item" v-for="item in album" :key="item.id">
					<view class="album-image">
						<image :src="item.cover" mode="aspectFill"></image>
					</view>
					<view class="album-info">
						<view class="album-text">专辑</view>
						<view class="album-name">{{item.name}}</view>
						<text class="iconfont iconiconfontjiantou4"></text>
					</view>
				</view>
			</view>
		</view>
		<!-- 专辑信息 结束 -->
		<!-- 最热评论 开始 -->
		<view class="comment-wrap hot" v-if="hot.length">
			<!-- 标题 -->
			<view class="comment-title">
				<text class="iconfont iconhot1"></text>
				<text class="title-text">最热评论</text>
			</view>
			<!-- 评论内容 -->
			<view class="comment-list">
				<view class="comment-item" v-for="item in hot" :key="item.id">
					<view class="user-info">
						<view class="user-avatar">
							<image :src="item.user.avatar" mode="widthFix" />
						</view>
						<view class="user-name">
							<view class="user-nikename">{{item.user.name}}</view>
							<view class="user-time">{{item.cnTime}}</view>
						</view>
						<view class="user-badge">
							<image v-for="item2 in item.user.title" :key="item2.icon" :src="item2.icon" />
						</view>
					</view>
					<view class="comment-conent">
						<text class="comment-text">{{item.content}}</text>
						<text class="iconfont icondianzan">{{item.size}}</text>
					</view>
				</view>
			</view>
		</view>
		<!-- 最热评论 结束 -->
		<!-- 最新评论 start -->
		<view class="comment-wrap new" v-if="comment.length">
			<!-- 标题 -->
			<view class="comment-title">
				<text class="iconfont iconpinglun"></text>
				<text class="title-text">最新评论</text>
			</view>
			<!-- 评论内容 -->
			<view class="comment-list">
				<view class="comment-item" v-for="item in comment" :key="item.id">
					<view class="user-info">
						<view class="user-avatar">
							<image :src="item.user.avatar" mode="widthFix" />
						</view>
						<view class="user-name">
							<view class="user-nikename">{{item.user.name}}</view>
							<view class="user-time">{{item.cnTime}}</view>
						</view>
						<view class="user-badge">
							<image v-for="item2 in item.user.title" :key="item2.icon" :src="item2.icon" />
						</view>
					</view>
					<view class="comment-conent">
						<text class="comment-text">{{item.content}}</text>
						<text class="iconfont icondianzan">{{item.size}}</text>
					</view>
				</view>
			</view>
		</view>
		<!-- 新评论 end -->
		<!-- 下载图片 开始 -->
		<view class="download">
			<view class="download_btn" @click="handelDownload">下载图片</view>
		</view>
		<!-- 下载图片 结束 -->
	</view>
</template>

<script>
	import moment from 'moment';
	import swiperAction from '@/components/swiperAction.vue'
	moment.locale("zh-cn");
	export default {
		data() {
			return {
				//图片信息对象，包含头像，图片。。。
				imgDetail:{},
				//专辑数据
				album:[],
				//热门评论
				hot:[],
				//最新评论
				comment:[],
				//图片索引
				imgIndex:0
			}
		},
		components:{
			swiperAction
		},
		onLoad() {
			//console.log(getApp().globalData);
			const {imgIndex} = getApp().globalData;
			this.imgIndex = imgIndex;
			this.getData();
			
		},
		methods: {
			//下载图片
			async handelDownload(){
				//uni.downloadFile
				//uni.saveImageToPhotosAlbum
				
				await uni.showLoading({
					title:"下载中"
				})
				
				//1:将远程文件下载到小程序中
				const result1 = await uni.downloadFile({
					url:this.imgDetail.img
				})
				const {tempFile} = result1[1];
				
				//将小程序内存中临时文件保存到本地
				
				const result2 = await uni.saveImageToPhotosAlbum({
					filePath:tempFile
				})
				//console.log(result2);
				
				//提示用户下载成功
				uni.hideLoading();
				await uni.showToast({
					title:"下载成功"
				})
			},
			//给当前页面赋值
			getData(){
				
				const {imgList} = getApp().globalData;
				this.imgDetail = imgList[this.imgIndex];
				
				//解决开始进去没有数据时候图片属性为undefine导致报错
				if(this.imgDetail.thumb.indexOf("imageMogr2") !== -1){
					this.imgDetail.newThumb = this.imgDetail.thumb;
				}else{
					this.imgDetail.newThumb = this.imgDetail.thumb + this.imgDetail.rule.replace('$<Height>',360);
				}
				//解决热门图片没有作者和头像问题
				if(!this.imgDetail.user){
					//this.imgDetail.user.avatar = this.imgDetail.newThumb;
					//this.imgDetail.user.name = "yuan";
					var user = new Object();
					user.avatar = this.imgDetail.newThumb;
					user.name = "yuan";
					this.imgDetail.user = user;	
				}
				
				//处理时间
				this.imgDetail.cnTime = moment(this.imgDetail.atime*1000).fromNow();
				
				//获取评论
				this.getComments(this.imgDetail.id);
			},
			//滑动事件
			handelSwiperAction(e){
				console.log(e)
				/*
				 1 用户 左滑  imgIndex++
				 2 用户 右滑 imgIndex--
				 3 不能无限制滑动 判断 数组是否越界
				 4 左滑 e.direction == "left"&& this.imgIndex < this.imgList.length-1
				 5 右滑 e.direction == "right"&& this.imgIndex > 0
				*/
			   const {imgList} = getApp().globalData;
			   
			   if(e.direction === "left" && this.imgIndex < imgList.length-1){
				   this.imgIndex++;
				   this.getData();
			   }else if(e.direction === "right"&& this.imgIndex > 0){
				   this.imgIndex--;
				   this.getData();
			   }else{
				   uni.showToast({
				   	title:"没有更多数据了",
					icon:"none"
				   })
			   }
			},
			getComments(id){
				this.request({
					url:`http://157.122.54.189:9088/image/v2/wallpaper/wallpaper/${id}/comment`
				}).then(result=>{
					console.log(result);
					/* if(!result.res.album && result.res.hot.length === 0 &&result.res.comment.length === 0){
						uni.showToast({
							title:"没有相关评论!",
							icon:"none"
						});
						return;
					} */
					this.album = result.res.album;
					this.hot = result.res.hot;
					this.comment = result.res.comment;
					
					result.res.hot.forEach(
					    v => (v.cnTime = moment(v.atime * 1000).fromNow())
					);
					result.res.comment.forEach(
					    v => (v.cnTime = moment(v.atime * 1000).fromNow())
					);
				})
			}
		}
	}
</script>

<style lang="scss" scoped>
	.user_info{
		display: flex;
		padding: 20rpx;
		.user_icon{
			padding: 0 20rpx;
			image{
				width: 88rpx;
				height: 88rpx;
				border-radius: 50%;
				
			}
		}
		.user_desc{
			.user_name{
				color: #000000;
				font-weight: 600;
			}
			.user_time{
				padding: 10rpx 0;
				color: #C0C0C0;
				font-size: 24rpx;
			}
		}
	}
	.user_rank{
		display: flex;
		height: 80rpx;
		border-bottom: 5px solid #EEEEEE;
		.rank{
			flex: 1;
			display: flex;
			justify-content: center;
			align-items: center;
			.iconfont{
				padding: 0 10rpx;
			}
		}
		.user_collect{
			flex: 1;
			display: flex;
			justify-content: center;
			align-items: center;
			.iconfont{
				padding: 0 10rpx;
			}
		}
	}
	.album-wrap{
		padding: 10rpx;
		.album-title{
			padding: 10rpx 0;
		}
		.album-list{
			.alnum-item{
				display: flex;
				padding: 10rpx 0;
				border-bottom: 10rpx solid #EEEEEE;
				.album-image{
					flex: 1;
					image{
						width: 180rpx;
						height: 180rpx;
					}
				}
				.album-info{
					flex: 3;
					padding-left: 20rpx;
					position: relative;
					.album-text{
						color: #FFFFFF;
						background-color: $color;
						padding: 20rpx;
						font-size: 24rpx;
						width: 90rpx;
						height: 50rpx;
						display: flex;
						justify-content: center;
						align-items: center;
					}
					.iconfont{
						position: absolute;
						top: 50%;
						right: 0%;
						transform: translateY(-50%);
					}
					.album-name{
						color: #000;
					}
				}
			}
		}
	}
	/* 最热评论 */
	.comment-wrap {
	  padding: 10rpx;
	  .comment-title {
		text.iconfont.iconhot1 {
		  font-size: 40rpx;
		  color: #ff1200;
		}
		text.title-text {
		  margin-left: 10rpx;
		  font-weight: 600;
		  color: #000;
		}
	  }

	  .comment-list {
		.comment-item {
		  padding-top: 20px;
		  border-bottom: 15rpx solid #eee;
		  .user-info {
			display: flex;
			.user-avatar {
			  width: 15%;
			  image {
			  }
			}

			.user-name {
			  flex: 1;
			  margin-left: 20rpx;
			  .user-nikename {
				color: #666;
			  }

			  .user-time {
				color: #ccc;
				font-size: 24rpx;
			  }
			}
			.user-badge {
			  image {
				display: inline-block;
				margin-right: 10rpx;
				width: 40rpx;
				height: 40rpx;
			  }
			}
		  }

		  .comment-conent {
			display: flex;
			justify-content: space-between;
			margin-left: 15%;
			padding: 20rpx;
			text.comment-text {
			  color: #000;
			}

			text.iconfont.icondianzan {
			  font-size: 38rpx;
			}
		  }
		}
	  }
	}
	/* 最热评论 */
	
	.comment-wrap.new {
	  .iconfont {
	    color: #92bdd8;
	    font-size: 40rpx;
	  }
	}
	
	.download{
		height: 120rpx;
		display: flex;
		align-items: center;
		justify-content: center;
		.download_btn{
			width: 90%;
			height: 80%;
			display: flex;
			align-items: center;
			justify-content: center;
			background-color: $color;
			color: #FFFFFF;
			font-size: 50rpx;
			font-weight: 600;
		}
	}
</style>
