<template>
	<view @click="handelClick">
		<slot></slot>
	</view>
</template>

<script>
	export default{
		props:{
			list:Array,
			index:Number
		},
		mounted() {
			//console.log(this.list);
			//console.log(this.index);
		},
		methods:{
			handelClick(){
				//console.log("yaun")
				//1:数据缓存
				getApp().globalData.imgList = this.list;
				getApp().globalData.imgIndex = this.index;
				//2：实现页面跳转
				uni.navigateTo({
					url:"/pages/imgDetail/imgDetail"
				})
			}
		}
	}
</script>

<style>
</style>
