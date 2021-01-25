<template>
	<view>
		<uni-card
			:title=sportsType
			:is-shadow="true"
			:extra=sportsLeague
			note="Life lies in sport."
		>
			<view class="uni-flex uni-row" style="height:140rpx;">
			  <view class="margin-0 uni-flex uni-column" style="width: 100rpx;">
				  <view class="margin-0 flex-item">
				  	<text>{{gameState}}</text>
				  </view>
				  <view class="margin-0 flex-item" style="padding-top: 10rpx; padding-left: 20rpx;">
				  	<text>Q1</text>
				  </view>
			  </view>
			  <view class="uni-flex uni-row" style="padding-right: 30rpx;padding-left: 100rpx; width: 240rpx;">
				<view class="flex-item uni-column">
				  <view class="margin-0 uni-flex">
					<image src="./static/Lakers.png" style="width: 40rpx; height: 40rpx;"></image>
					<text>{{pAName}}</text>
				  </view>
				  <view class="margin-0 uni-flex" style="padding-left: 10rpx;">
					  vs
				  </view>
				  <view class="margin-0 uni-flex">
					<image src="./static/Celts.png" style="width: 40rpx; height: 40rpx;"></image>
				  	<text>{{pBName}}</text>
				  </view>
				</view>
			  </view>
			  <view class="uni-flex uni-row" style="padding-right: 80rpx;padding-left: 50rpx; width: 50rpx;">
				  <view class="flex-item uni-column">
					<view class="margin-0 uni-flex-sub">
						<text>{{pAScore}}</text>
					</view>
					<view class="margin-0 uni-flex" style="padding-left: 10rpx;">
						|
					</view>
					<view class="margin-0 uni-flex-sub">
						<text>{{pBScore}}</text>
					</view>
				  </view>
			  </view>
			  <view class="margin-0 uni-flex-sub uni-column" style="padding-top: 15rpx;">
				  <view class="margin-0 flex-item">
					  <image src="./static/TV.png" style="width: 40rpx; height: 40rpx;"></image>
				  </view>
				  <view class="margin-0 flex-item">
					  <text >{{tvChannel}}</text>
				  </view>
			  </view>
			</view>
		</uni-card>
	</view>
</template>

<script>
	import uniCard from '@/components/uni-card/uni-card.vue'
	export default {
		components: {uniCard},
		name: "SportsDetail",
		props: {
			title: {
				type: String,
				default: ''
			},
			subTitle: {
				type: String,
				default: ''
			},
			extra: {
				type: String,
				default: ''
			},
			note: {
				type: String,
				default: ''
			},
			thumbnail: {
				type: String,
				default: ''
			},
			mode: {
				type: String,
				default: 'basic'
			},
			isFull: {
				// 内容区域是否通栏
				type: Boolean,
				default: false
			},
			isShadow: {
				// 是否开启阴影
				type: [Boolean, String],
				default: false
			},
			sportsType: {
				type: String,
				default: ''
			},
			sportsLeague: {
				type: String,
				default: ''
			},
			gameStartTime: {
				type: String,
				default: ''
			},
			gameClock: {
				type: String,
				default: ''
			},
			gamePeriod: {
				type: String,
				default: ''
			},
			gameState: {
				type: String,
				default: ''
			},
			pAName: {
				type: String,
				default: ''
			},
			pAScore: {
				type: String,
				default: ''
			},
			pBName: {
				type: String,
				default: ''
			},
			pBScore: {
				type: String,
				dafault: ''
			},
			tvChannel: {
				type: String,
				default: ''
			}
		},
		data() {
			return {

			}
		},
		methods: {

		}
	}
</script>

<style scoped>
	.uni-card {
		/* #ifndef APP-NVUE */
		display: flex;
		flex: 1;
		box-shadow: 0 0 0 rgba(0, 0, 0, 0);
		/* #endif */
		margin: 12px 15px;
		background-color: #ffffff;
		position: relative;
		flex-direction: column;
		border-radius: 5px;
		overflow: hidden;
	}
</style>
