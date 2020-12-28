<template>
	<scroll-view  scroll-y="true" @scrolltolower="handelToLower" class="album_view" >
		<!-- 轮播 开始 -->
		<view class="album_swiper">
			<swiper class="swiper" indicator-dots=true autoplay=true interval="2000" duration="500">
				<swiper-item v-for="item in banner" :key="item.id">
					<image :src="item.thumb"></image>
				</swiper-item>
			</swiper>
		</view>
		<!-- 轮播 结束 -->
		
		<!-- 列表 开始 -->
		<view class="album_list">
			<navigator 
			class="album_item"
			v-for="item in album"
			:key="item.id"
			:url="`/pages/album/album?id=${item.id}`"
			>
				<view class="album_image">
					<image :src="item.cover" mode="aspectFill"></image>
				</view>
				<view class="album_info">
					<view class="album_name">
						{{item.name}}
					</view>
					<view class="album_desc">
						{{item.desc}}
					</view>
					<view class="album_btn">
						<view class="album_attention"> + 关注</view>
					</view>
				</view>
			</navigator>
		</view>
		<!-- 列表 结束 -->
		<view class="albun_last" v-if="!hasmore">
			没有更多信息了
		</view>
	</scroll-view>
</template>

<script>
	export default {
		data() {
			return {
				params:{
					limit:30,
					order:"new",
					skip:0
				},
				//轮播数组
				banner:[],
				//列表数组
				album:[],
				hasmore:true
			}
		},
		mounted() {
			uni.setNavigationBarTitle({
			    title: '专辑'
			});
			this.getList();
		},
		methods: {
			getList(){
				this.request({
					url:"http://157.122.54.189:9088/image/v1/wallpaper/album",
					data:this.params
				}).then(result=>{
					//console.log(result)
					if(result.res.album.length === 0){
						this.hasmore = false;
						return;
					}
					if(this.banner.length === 0){
						this.banner = result.res.banner;
					}
					
					this.album = [...this.album,...result.res.album];
				})
			},
			handelToLower(){
				if(this.hasmore){
					this.params.skip += this.params.limit;
					this.getList();
				}else{
					uni.showToast({
						title:"没有数据了",
						icon:"none"
					})
				}
			}
		}
	}
</script>

<style lang="scss" scoped>
	.album_view{
		//height  屏幕高度-标题高度
		height: calc(100vh - 36px);
	}
	.album_swiper{
		.swiper{
			height: calc(750rpx / 2.3);
			image{
				height: 100%;
			}
		}
	}
	.album_list{
		padding: 10rpx;
		.album_item{
			padding: 10rpx 0;
			display: flex;
			border-bottom: 1px solid #CCCCCC;
			.album_image{
				flex: 1;
				padding: 10rpx;
				image{
					width: 200rpx;
					height: 200rpx;
				}
			}
			.album_info{
				flex: 2;
				padding: 0 10rpx;
				overflow: hidden;
				.album_name{
					font-size: 30rpx;
					color: #000000;
					padding: 10rpx 0;
				}
				.album_desc{
					padding: 10rpx 0;
					font-size: 24rpx;
					
					text-overflow: ellipsis;
					overflow: hidden;
					white-space: nowrap;
				}
				.album_btn{
					padding: 10rpx 0;
					display: flex;
					justify-content: flex-end;
					.album_attention{
						color: $color;
						border: 1px solid $color;
						padding: 10rpx;
					}
				}
			}
		}
	}
	.albun_last{
		padding: 10rpx;
		font-size: 24rpx;
		color: #808080;
	}
</style>
