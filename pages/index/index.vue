<template>
	<view >
		<!--导航栏 -->
		<scroll-view scroll-x class="navBar">
			<view class="nav_item" 
			:class="index==navIndex ? 'active' : ''" v-for="(item,index) in navArr" 
			@click="clickNav(index,item.id)"
			:key="item.id"
			>{{item.classname}}</view>			
		</scroll-view>
		<!-- 新闻列表 -->
		<view class="content" >
			<view class="row" v-for="item in newsArr" :key="item.id">
				<newsItem :item="item" @click.native="goDetail(item)"></newsItem>
			</view>
		</view>
		<!-- 数据加载完了，没有更多 -->
		<view class="nodata" v-if="!newsArr.length">
			<image src="../../static/images/nodata.png" mode="widthFix"></image>
		</view>
		
		<view class="loading" v-if="newsArr.length">			
			<view v-if="loading==1">数据加载中...</view>
			<view v-if="loading==2">没有更多了~~</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				navIndex:0,
				navArr:[],
				newsArr:[],
				currentId:50,
				currentPage:1,
				loading:0       //0默认  1加载中  2没有更多了
			}
		},
		onLoad() {
			this.getNavData();
			this.getNewsData();
		},
		onReachBottom(){
			// console.log("到底部了")
			if(this.loading==2){
				return;
			}
			this.currentPage++;
			this.loading=1;
			this.getNewsData();
		},
		
		methods: {
			//点击导航切换
			clickNav(index,id){
				this.navIndex=index;	
				this.currentPage=1;				
				this.newsArr=[]
				this.loading=0;
				this.currentId=id;
				this.getNewsData();
			},
			
			//跳转到详情页
			goDetail(item){				
				uni.navigateTo({
					url:`/pages/detail/detail?cid=${item.classid}&id=${item.id}`
				})
			},
			
			//获取导航列表数据
			getNavData(){
				uni.request({
					url:"https://ku.qingnian8.com/dataApi/news/navlist.php",
					success:res=>{
						// console.log(res) //打印数据
						this.navArr=res.data
					}
				})
			},
			
			//获取新闻列表数据
			getNewsData(){
				uni.request({
					url:"https://ku.qingnian8.com/dataApi/news/newslist.php",
					data:{						
						cid:this.currentId,
						page:this.currentPage
					},
					success:res=>{
						// console.log(res)	//打印数据
						if(res.data.length==0){
							this.loading=2
						}
						this.newsArr=[...this.newsArr,...res.data]
					}
				})
			}
			
		}
	}
</script>

<style lang="scss" scoped>
.navBar{
	height: 100rpx;
	background: #F7F8FA;
	//文章不换行
	white-space: nowrap;
	position: fixed;
	top:var(--window-top);
	left:0;
	z-index: 10;
	// 消除H5页面的滚动条
	/deep/ ::-webkit-scrollbar {
		width: 4px !important;
		height: 1px !important;
		overflow: auto !important;
		background: transparent !important;
		-webkit-appearance: auto !important;
		display: block;
	}
	.nav_item{
		font-size: 40rpx;
		display: inline-block;
		line-height: 100rpx;
		padding:0 30rpx;
		color:#333;		
		&.active{
			color:#31C27C;
		}
	}
}

.content{
	padding:30rpx;
	padding-top:130rpx;	
	.row{
		border-bottom:1px dotted #efefef;
		padding:20rpx 0;
	}
}
.nodata{
	display: flex;
	justify-content: center;
	image{
		width: 360rpx;
	}
}
.loading{
	text-align: center;
	font-size: 26rpx;
	color:#888;
	line-height: 2em;
}
</style>
