<template>
	<view class="home">
		<custom-head-bar id="customHeadBar"></custom-head-bar>
		<view class="wrapper">
			<view class="infoModel">
				<view class="left">免费配送

				</view>
				<navigator url="/pages/order/order" class="right">
					<u-icon name="order" color="#576b95" size="22"></u-icon>
					我的订单
				</navigator>
			</view>
			<view class="scrollLayout">
				<view class="leftScroll">
					<scroll-view scroll-y class="sContent" :scroll-top="leftScrollValue">
						<view class="navitem" :class="index==navIdx?'active':''" 
						v-for="(item,index) in dataList"
						:key="item.id"
							@click="clickNav(index)">{{item.name}}</view>
					</scroll-view>
				</view>
				<view class="rightScroll">
				
					<navigator url="/pages/search/search" class="searchView">
						<u-icon name="search" size="22" color="#576b95"></u-icon>
						搜索
					</navigator>
					<scroll-view @scroll="rightScrollEnt" :scroll-top="rightScrollValue" scroll-y scroll-with-animation
						class="sContent">
						<view class="productView" v-for="item in dataList">
							<u-sticky :customNavHeight="0" zIndex="2">
								<view class="proTitle">{{item.name}}</view>
							</u-sticky>
							<view class="proContent">
								<view class="proitem" v-for="pro in item.children">
									<product-item :item="pro"></product-item>
								</view>
							</view>
						</view>
					</scroll-view>
				</view>

			</view>
		</view>


		<car-layout v-if="buyNum>0"></car-layout>
	</view>
</template>

<script>
	import {
		mapState,
		mapMutations,
		mapGetters
	} from "vuex"
	export default {
		data() {
			return {
				navIdx: 0,
				leftScrollValue: 0,
				rightScrollValue: 0,
				leftHitArr: [],
				rightHitArr: [],
				foldState: false,
				dataList: [{
					id: 1,
					name: "豆干制品",
					children: [{
						id: 11,
						name: "卫龙辣条",
						price: 10,
						before_price: 22,
						thumb: "https://mp-c422c6b7-799d-4ff5-9531-5051a0481131.cdn.bspapp.com/cloudstorage/83562e26-cfac-4cec-8f51-9ae6986942af.jpg",
						numvalue:0
					}, {
						id: 12,
						name: "卫龙大面筋",
						price: 5,
						before_price: 12,
						thumb: "https://mp-c422c6b7-799d-4ff5-9531-5051a0481131.cdn.bspapp.com/cloudstorage/30569d48-bb94-40de-8d2b-a3be99d710cd.jpg",
						numvalue:0
					}]
				}, {
					id: 2,
					name: "饼干糕点",
					children: [{
						id: 21,
						name: "丹麦曲奇",
						price: 25,
						before_price: 36,
						thumb: "https://mp-3309c116-4743-47d6-9979-462d2edf878c.cdn.bspapp.com/cloudstorage/6758e11c-949b-48c5-ae69-ddad030c2f94.png",
						numvalue:0
					}]
				}, {
					id: 3,
					name: "酒水饮料",
					children: [{
						id: 31,
						name: "韩国烧酒",
						price: 18,
						before_price: 29,
						thumb: "https://mp-3309c116-4743-47d6-9979-462d2edf878c.cdn.bspapp.com/cloudstorage/b1a12bee-0602-4cb5-927d-b2b246700e89.jpeg",
						numvalue:0
					}]
				}]
			}
		},
		onLoad() {
			this.$nextTick(() => {
				this.getHeightArr();
			})
		},
		computed: {
			...mapGetters(["buyNum"])
		},
		methods: {
			...mapMutations(["setFoldState"]),
			//点击导航菜单
			clickNav(index) {
				if (this.navIdx == index) return;
				this.navIdx = index;
				if (this.timeout) {
					clearTimeout(this.timeout);
				}
				this.timeout = setTimeout(() => {
					this.leftScrollValue = this.leftHitArr[index];
					this.rightScrollValue = this.rightHitArr[index];
				}, 100)
			},
			//获取滚动条内容高度
			getHeightArr() {
				let selectorQuery = uni.createSelectorQuery();
				let customHeadBar;
				//获取自定义导航高度				
				selectorQuery.select("#customHeadBar").boundingClientRect(rect => {
					customHeadBar = rect.height;
				}).exec()


				//左侧滚到区域的节点组
				selectorQuery.selectAll(".navitem").boundingClientRect(rects => {
					this.leftHitArr = rects.map(item => item.top - customHeadBar - 40)
				}).exec()
				console.log(this.leftHitArr);
				//右侧滚到区域的节点组
				selectorQuery.selectAll(".productView").boundingClientRect(rects => {
					this.rightHitArr = rects.map(item => item.top - customHeadBar - 40)
				}).exec()

			},

			//监听右侧滚动条的改变
			rightScrollEnt(e) {
				let scrollTop = Math.ceil(e.detail.scrollTop);
				let idx = this.rightHitArr.findIndex((value, index, arr) => scrollTop >= value && scrollTop < arr[index +
					1])
				this.navIdx = idx;
				this.leftScrollValue = this.leftHitArr[idx];

				if (scrollTop < 300) {
					this.setFoldState(false)
				}
				if (scrollTop > 400) {
					this.setFoldState(true)
				}
			}

		}
	}
</script>

<style lang="scss" scoped>
	.home {
		height: 100vh;
		display: flex;
		flex-direction: column;

		.wrapper {
			flex: 1;
			background: #fff;
			border-radius: 10px 10px 0 0;
			margin-top: -10px;
			position: relative;
			z-index: 2;
			overflow: hidden;

			.infoModel {
				color: $text-font-color-1;
				@include flex-box();
				height: 50px;
				background: #fff;
				padding: 0 30rpx;
				font-size: 32rpx;
				border-bottom: 1px solid $border-color;

				.right {
					@include flex-box();
					color: $brand-theme-color-aux;
				}
			}

			.scrollLayout {
				height: calc(100% - 100rpx);
				@include flex-box();

				.leftScroll {
					height: 100%;
					width: 190rpx;
					border-right: 1px solid $border-color;
					background: $page-bg-color;

					.navitem {
						font-size: 30rpx;
						padding-left: 30rpx;
						line-height: 100rpx;
						color: $text-font-color-2;
						position: relative;

						&.active {
							color: $text-font-color-1;
							background: #fff;

							&::after {
								content: "";
								width: 6rpx;
								height: 30rpx;
								background: $brand-theme-color;
								position: absolute;
								left: 0;
								top: 50%;
								transform: translateY(-50%);
							}
						}
					}
				}

				.rightScroll {
					height: 100%;
					flex: 1;
					position: relative;

					.searchView {
						position: absolute;
						top: 0;
						right: 30rpx;
						z-index: 3;
						@include flex-box-set();
						height: 90rpx;
						color: $brand-theme-color-aux;
					}

					.productView {
						padding: 0 30rpx;

						.proTitle {
							line-height: 90rpx;
							font-size: 30rpx;
							background: #fff;
						}
					}
				}

				.sContent {
					height: 100%;
				}
			}
		}

	}
</style>
