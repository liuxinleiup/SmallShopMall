<template>
	<view class="paypage">
		<delivery-layout></delivery-layout>
		
		<view class="goodsList">
			<goods-list></goods-list>
		</view>
		
		<view class="paytype">
			<view class="box active" v-for="item in payType">
				<u-icon :name="item.value == 'alipay'?'zhifubao':''" color="#1578FF" class="icon" size="26"></u-icon>
				<u-icon :name="item.value == 'wxpay'?'weixin-fill':''" color="#04C15F" class="icon" size="26"></u-icon>
				<text class="font">{{item.name}}</text>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				payType:[{
					name:"支付宝",
					value:"alipay"
				},{
					name:"微信",
					value:"wxpay"
				}]
			};
		}
	}
</script>

<style lang="scss">
page{
	background: $page-bg-color;
}
.paypage{
	padding:30rpx;
	.goodsList{
		margin-top:30rpx;
	}
	.paytype{
		@include flex-box();
		.box{
			width: 49%;
			height: 75rpx;
			background: #fff;
			border-radius: 10rpx;
			@include flex-box-set();
			border:1px solid #fff;
			.font{
				padding-left:10rpx;
			}
		}
		.box:first-child.active{
			border-color:#1578FF;
			color:#1578FF
		}
		.box:last-child.active{
			border-color:#04C15F;
			color:#04C15F			
		}
	}
}
</style>
