<template>
	<scroll-view  scroll-y="true" @scrolltolower="handelToLower" class="recommends_view" v-if="recommends.length > 0">
		<!-- 推荐 开始 -->
		<view class="recomend_wrap">
			<navigator 
				class="recommend_item"
				v-for="item in recommends"
				:key="item.id"
				:url="`/pages/album/album?id=${item.target}`"
				>
				<image mode="widthFix" :src="item.thumb"></image>
			</navigator>
		</view>
		<!-- 推荐 结束 -->
		<!-- 月份 开始 -->
		<view class="monthes_wrap">
			<view class="monthes_title">
				<view class="monthes_title_info">
					<view class="monthes_info">
						<text>{{monthes.DD}} /</text>
						{{monthes.MM}} 月
					</view>
					<view class="monthes_text">{{monthes.title}}</view>
				</view>
				<view class="monthes_title_more">更多 > </view>
			</view>
			<view class="monthes_content">
				<view 
				class="monthes_item"
				v-for="(item,index) in monthes.items"
				:key="item.id">
					<go-detail :list="monthes.items" :index="index">
						<image :src="item.thumb + item.rule.replace('$<Height>',360)" mode="aspectFill"></image>
					</go-detail>
				</view>
			</view>
		</view>
		<!-- 月份 结束 -->
		
		<!-- 热门 开始 -->
		<view class="hots_wrap">
			<view class="hots_title">
				<text>热门</text>
			</view>
			<view class="hots_content">
				<view class="hots_item" v-for="(item,index) in hots" :key="item.id">
					<go-detail :list="hots" :index="index">
						<image :src="item.thumb" mode="widthFix"></image>
					</go-detail>
				</view>
			</view>
		</view>
		<!-- 热门结束 -->
		<view v-if="hasMore">没有更多数据了</view>
	</scroll-view>
	
</template>

<script>
	import moment from 'moment'
	import goDetail from "@/components/goDetail.vue"
	export default {
		data() {
			return {
				//推荐列表
				recommends:[],
				//月份
				monthes:{},
				//热门
				hots:[],
				//请求参数
				params:{
					limit:30,
					order:"hot",
					skip:0
				},
				hasMore:false
			}
		},
		components:{
			goDetail
		},
		methods: {
			//滚动条触底时间
			handelToLower(){
				//console.log("wo dao di l ")
				/* 
					1:修改请求参数 skip += limit
					2:从新发请求
					3：数据合并
				*/
			   if(!this.hasMore){
				   this.params.skip += this.params.limit;
				   this.getList();
			   }else{
				   uni.showToast({
				   	title:"没有数据了",
					icon:"none"
				   })
			   }
			},
			//获取接口数据
			getList(){
				this.request({
					url:'http://157.122.54.189:9088/image/v3/homepage/vertical',
					data:this.params
				}).then(result=>{
					//console.log(result)
					if(result.res.vertical.length === 0){
						this.hasMore = true;
						return;
					}
					if(this.recommends.length === 0){
						//推荐模块
						this.recommends = result.res.homepage[1].items;
						//月份模块
						this.monthes = result.res.homepage[2];
						//将时间戳改为对应时间
						this.monthes.MM = moment(this.monthes.stime).format("MM");
						this.monthes.DD = moment(this.monthes.stime).format("DD");
						//console.log(this.monthes)
					}
					
					//热门模块
					//数组拼接
					this.hots = [...this.hots,...result.res.vertical];
				})
			}
		},
		mounted() {
			this.getList();
		}
	}
</script>

<style lang="scss" scoped>
	.recommends_view{
		//height  屏幕高度-标题高度
		height: calc(100vh - 36px);
	}
	.recomend_wrap{
		display: flex;
		flex-wrap: wrap;
		.recommend_item{
			width: 50%;
			border: 5rpx solid #FFFFFF;
		}
	}
	.monthes_wrap{
		.monthes_title{
			display: flex;
			justify-content: space-between;
			padding: 20rpx;
			.monthes_title_info{
				color: $color;
				font-size: 30rpx;
				font-weight: 600;
				display: flex;
				.monthes_info{
					text{
						font-size: 36rpx;	
					}
				}
				.monthes_text{
					font-size: 34rpx;
					color: #666;
					margin-left: 30rpx;
				}
			}
			.monthes_title_more{
				font-size: 24rpx;
				color: $color;
			}
		}
		.monthes_content{
			display: flex;
			flex-wrap: wrap;
			.monthes_item{
				width: 33.33%;
				border: 5rpx solid #FFFFFF;
			}
		}
	}
	.hots_wrap{
		.hots_title{
			padding: 20rpx;
			text{
				border-left: 10rpx solid $color;
				font-size: 24rpx;
				font-weight: 600;
				padding-left: 10rpx;
			}
		}
		.hots_content{
			display: flex;
			flex-wrap: wrap;
			.hots_item{
				width: 33.33%;
				border: 5rpx solid #FFFFFF;
				image{
					
				}
			}
		}
	}
</style>
