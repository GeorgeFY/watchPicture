<template>
	<view class="category_tab">
		<view class="category_tab_title">
			<view class="title_inner">
				<uni-segmented-control
					:current="current" 
					:values="items.map(v=>v.title)" 
					@clickItem="onClickItem" 
					style-type="text" 
					active-color="#d4237a">
				</uni-segmented-control>
			</view>
			<view class="iconfont iconsearch"></view>
		</view>
		<scroll-view scroll-y="true" enable-flex="true" @scrolltolower="handelTolower" class="category_tab_content">
			<view class="cate_item"
			v-for="item in vertical"
			:key="item.id"
			>
				<image :src="item.thumb" mode="widthFix"></image>
			</view>
		</scroll-view>
	</view>
</template>

<script>
	
	export default {
		data() {
			return {
				items: [
					{title:"最新",order:"new"},
					{title:"热门",order:"hot"}
				],
				current: 0,
				params:{
					limit:15,
					skip:0,
					order:"new"
				},
				id:0,
				//页面渲染数组
				vertical:[],
				hasmore:true
			}
		},
		onLoad(options) {
			this.id = options.id;
			this.getList();
		},
		methods: {
			onClickItem(e) {
				/*
				 跟进被点击切换标题
				 跟进被点击切换order
				 从新发请求
				*/
				if (this.current !== e.currentIndex) {
					this.current = e.currentIndex;
				}else{
					//点击相同的标题
					return;
				}
				this.params.skip = 0;
				this.vertical = [];
				this.params.order = this.items[e.currentIndex].order;
				this.getList();
			},
			getList(){
				//console.log("yuan")
				this.request({
					url:`http://157.122.54.189:9088/image/v1/vertical/category/${this.id}/vertical`,
					data:this.params
				}).then(result=>{
					//console.log(result)
					if(result.res.vertical.length === 0){
						this.hasmore = false;
						uni.showToast({
							title:"没有更多数据了",
							icon:"none"
						})
						return;
					}
					this.vertical = [...this.vertical,...result.res.vertical];
				})
			},
			handelTolower(){
				if(this.hasmore){
					this.params.skip += this.params.limit;
					this.getList();
				}else{
					uni.showToast({
						title:"没有跟多数据了",
						icon:"none"
					})
				}
			}
		},
		components:{
			
		}
	}
</script>

<style lang="scss" scoped>
	.category_tab{
		.category_tab_title{
			position: relative;
			.title_inner{
				width: 60%;
				margin: 0 auto;
			}
			.iconsearch{
				position: absolute;
				top: 50%;
				transform: translateY(-50%);
				right: 5%;
			}
		}
		.category_tab_content{
			display: flex;
			flex-wrap: wrap;
			height: calc(100vh - 36px);
			.cate_item{
				width: 33.33%;
				image{
					border: 5rpx solid #FFFFFF;
				}
			}
		}
	}
</style>
