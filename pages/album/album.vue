<template>
	<view>
		<!-- 专辑背景 开始 -->
		<view class="album_bg" v-if="Object.keys(album).length !== 0">
			<image :src="album.cover" mode="widthFix"></image>
			<view class="album_info">
				<view class="album_name">{{album.name}}</view>
				<view class="album_btn">关注专辑</view>
			</view>
		</view>
		<!-- 专辑背景 结束 -->
		
		<!-- 专辑 作者开始 -->
		<view class="album_author">
			<view class="album_author_info">
				<image mode="widthFix" :src="album.user.avatar"></image>
				<view class="author_name">{{album.user.name}}</view>
			</view>
			<view class="album_desc">
				<text>{{album.desc}}</text>
			</view>
		</view>
		<!-- 专辑 作者结束 -->
		
		<!-- 图片列表 开始 -->
		<view class="album_list">
			<view class="album_item" v-for="(item,index) in wallpaper" :key="item.id">
				<go-detail :list="wallpaper" :index="index">
					<image :src="item.thumb + item.rule.replace('$<Height>',360)" mode="aspectFill"></image>
				</go-detail>
			</view>
		</view>
		<!-- 图片列表结束 -->
	</view>
</template>

<script>
	import goDetail from "@/components/goDetail.vue"
	export default {
		data() {
			return {
				params:{
					limit:9,
					order:"new",
					skip:0,
					//first==1返回所有值，first==0返回值只有wallpaper
					first:1
				},
				id:-1,
				album:{},
				wallpaper:[],
				hasmore:true
			}
		},
		components:{
			goDetail
		},
		onLoad(options) {
			console.log(options)
			this.id = options.id;
			//this.id = "5d5f8e45e7bce75ae7fb8278";
			this.getList();
		},
		methods: {
			getList(){
				this.request({
					url:`http://157.122.54.189:9088/image/v1/wallpaper/album/${this.id}/wallpaper`,
					data:this.params
				}).then(result=>{
					console.log(result)
					if(Object.keys(this.album).length === 0){
						this.album = result.res.album;
					}
					if(result.res.wallpaper.length === 0){
						this.hasmore = false;
						uni.showToast({
							title:"没有更多数据了",
							icon:"none"
						})
						return;
					}
					this.wallpaper =[...this.wallpaper,...result.res.wallpaper];
				})
			}
		},
		onReachBottom() {
			console.log("wo dao di le l")
			if(this.hasmore){
				this.params.first = 0;
				this.params.skip += this.params.limit;
				this.getList();
			}else{
				uni.showToast({
					title:"没有更多数据了",
					icon:"none"
				})
			}
		}
	}
</script>

<style lang="scss" scoped>
	.album_bg{
		position: relative;
		image{
			
		}
		.album_info{
			position: absolute;
			width: 100%;
			left: 0;
			bottom: 0;
			display: flex;
			justify-content: space-between;
			height: 80rpx;
			align-items: center;
			color: #FFFFFF;
			padding: 0 15rpx;
			.album_name{
				font-size: 40rpx;
			}
			.album_btn{
				background-color: $color;
				width: 150rpx;
				height: 60rpx;
				display: flex;
				justify-content: center;
				align-items: center;
				border-radius: 10rpx;
			}
		}
	}
	.album_author{
		padding: 10rpx;
		.album_author_info{
			display: flex;
			padding: 10rpx 0;
			image{
				width: 50rpx;
			}
			.author_name{
				color: #000;
				margin-left: 15rpx;
			}
		}
		.album_desc{
			
		}
	}
	.album_list{
		display: flex;
		flex-wrap: wrap;
		.album_item{
			width: 33.33%;
			border: 1px solid #FFFFFF;
			image{
				height: 160rpx;
			}
		}
	}
</style>
