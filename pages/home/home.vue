<template>
	<view class="home_tab">
		<view class="home_tab_title">
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
		<view class="home_tab_content">
			<view v-if="current === 0">
				<homeRecommend></homeRecommend>
			</view>
			<view v-if="current === 1">
				<homeCategory></homeCategory>
			</view>
			<view v-if="current === 2">
				<homeNew></homeNew>
			</view>
			<view v-if="current === 3">
				<hoemAlbun></hoemAlbun>
			</view>
		</view>
	</view>
</template>

<script>
	import hoemAlbun from './home-album';
	import homeRecommend from './home-recommend';
	import homeCategory from './home-category';
	import homeNew from './home-new';
	export default {
		data() {
			return {
				items: [
					{title:"推荐"},
					{title:"分类"},
					{title:"最新"},
					{title:"专辑"}
				],
				current: 0
			}
		},
		methods: {
			onClickItem(e) {
				if (this.current !== e.currentIndex) {
					this.current = e.currentIndex;
				}
			}
		},
		components:{
			hoemAlbun,
			homeRecommend,
			homeCategory,
			homeNew
		},
		onLoad() {
			
		}
		
	}
</script>

<style lang="scss" scoped>
	.home_tab{
		.home_tab_title{
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
		.home_tab_content{
			
		}
	}
</style>
