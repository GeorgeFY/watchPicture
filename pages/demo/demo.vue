<template>
	<view 
	@touchstart="handelTouchstart"
	@touchend="handelTouchend">
		学习事情
	</view>
</template>

<script>
	/* 
		1:给容器绑定两个 触屏事件
		2：用户按下屏幕事件
			1：计入按下屏幕的时间   Data.now()
			2：记录按下屏幕的坐标
		3：用户离开屏幕事件
			1：记录离开屏幕的时间  Data.now()
			2：记录离开屏幕的坐标
		4：更具两个时间判断按下屏幕时间是否合法
		5：计算坐标距离 判断是否合法 判断滑动方向
	*/
	export default {
		data() {
			return {
				startTime:0,
				startX:0,
				startY:0
			}
		},
		methods: {
			//用户按下屏幕
			handelTouchstart(event){
				//console.log("按下")
				//console.log(event.changedTouches[0].clientX);
				//console.log(event.changedTouches[0].clientY);
				
				this.startTime = Date.now();
				this.startX = event.changedTouches[0].clientX;
				this.startY = event.changedTouches[0].clientY;
			},
			handelTouchend(event){
				//console.log("离开")
				//console.log(event.changedTouches[0].clientX);
				//console.log(event.changedTouches[0].clientY);
				
				const endTime = Date.now();
				const endX = event.changedTouches[0].clientX;
				const endY = event.changedTouches[0].clientY;
				
				//判断按下时间
				if(endTime - this.startTime > 2000){
					return;
				}
				
				//先判断用户滑动距离 是否合法  合法判断滑动方向
				//滑动方向
				let direction = "";
				if(Math.abs(endX - this.startX) >10){
					//判断滑动方向
					direction = endX - this.startX > 0 ? "right" : "left";
				}else{
					return;
				}
				console.log(direction);
			}
		}
	}
</script>

<style lang="scss" scoped>
	view{
		width: 100%;
		height: 250rpx;
		background-color: #4CD964;
	}
</style>
