<template>
	<scroll-view scroll-y="true" enable-flex="true" class="video_main" @scrolltolower="handelToLower">
		<view
		class="video_item"
		v-for="item in videowp"
		:key="item.id"
		@click="handelToVideo(item)"
		>
			<image mode="widthFix" :src="item.img"></image>
		</view>
	</scroll-view>
</template>

<script>
	export default {
		props:{
			urlobj:Object
		},
		mounted() {
			console.log(this.urlobj)
			this.getList();
		},
		watch:{
			urlobj(){
				//console.log("参数发送变化了")
				//console.log(this.urlobj)
				this.videowp = [];
				this.getList();
			}
		},
		data() {
			return {
				videowp:[],
				hasmore:"true"
			}
		},
		methods: {
			getList(){
				this.request({
					url:this.urlobj.url,
					data:this.urlobj.params
				}).then(result=>{
					console.log(result.res);
					if(result.res.videowp.length === 0){
						this.hasmore = false;
						uni.showToast({
							title:"没有更多数据了",
							icon:"none"
						})
						return;
					}
					this.videowp = [...this.videowp,...result.res.videowp];
				})
			},
			handelToLower(){
				if(this.hasmore){
					this.urlobj.params.skip += this.urlobj.params.limit;
					this.getList();
				}else{
					uni.showToast({
						title:"没有更多的数据了",
						icon:"none"
					})
				}
			},
			handelToVideo(item){
				//1:数据存储到全局共享
				//2:页面跳转
				
				getApp().globalData.video = item;
				uni.navigateTo({
					url:"/pages/videoPlay/index"
				})
			}
		}
	}
</script>

<style lang="scss" scoped>
	.video_main{
		display: flex;
		flex-wrap: wrap;
		height: calc(100vh - 36px);
		.video_item{
			width: 33.33%;
			border: 5rpx solid #FFFFFF;
			image{
				
			}
		}
	}
</style>
