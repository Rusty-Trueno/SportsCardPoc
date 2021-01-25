<template>
	<view>
		<uni-card
			title="Game Details"
			:is-shadow="true"
			extra="Upcoming game with your home team"
			note="Life lies in sport."
		>
			<view class="uni-flex uni-row" style="height:140rpx;">
			  <view class="margin-0 uni-flex uni-column" style="width: 300rpx;">
				  <view class="margin-0 flex-item">
					<image src="./static/Lakers.png" style="width: 40rpx; height: 40rpx;"></image>
				  	<text>{{pAName}}</text>
				  </view>
				  <view class="margin-0 flex-item" style="padding-top: 10rpx; padding-left: 20rpx;">
				  	<text>{{pAScore}}</text>
				  </view>
			  </view>
			  <view class="uni-flex uni-row" style="padding-left: 30rpx; width: 350rpx;">
				<view class="flex-item uni-column">
				  <view class="margin-0 uni-flex">
					<text>{{getTime}}</text>
				  </view>
				  <view v-if="inGame" class="margin-0 uni-flex" style="padding-left: 30rpx;">
				  	<text>{{gamePeriod}}</text>
				  </view>
				  <view v-if="inGame" class="margin-0 uni-flex" style="padding-left: 30rpx;">
				  	<text>{{gameClock}}</text>
				  </view>
				  <view v-if="!inGame" class="margin-0 uni-flex" style="padding-left: 30rpx;">
				  	<text>{{gameState}}</text>
				  </view>
				  <view class="margin-0 uni-flex" style="padding-left: 60rpx;">
					 <text>{{tvChannel}}</text>
				  </view>
				</view>
			  </view>
			  <view class="uni-flex uni-row" style="padding-left: 30rpx; width: 200rpx;">
				  <view class="flex-item uni-column">
					<view class="margin-0 uni-flex-sub">
						<text>{{pBName}}</text>
						<image src="./static/Celts.png" style="width: 40rpx; height: 40rpx;"></image>
					</view>
					<view class="margin-0 uni-flex-sub">
						<text>{{pBScore}}</text>
					</view>
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
		data() {
			return {
				gameSatrtMonth: 'Jan 25',
				gameStartTime: '5:06 PM',
				pAName: 'Buccanerrs',
				pAScore: '31',
				pBName: 'Packers',
				pBScore: '26',
				tvChannel: 'FOX',
				gameState: 'PreGame',
				gamePeriod: 'Q1',
				gameClock: '5:33',
				inGame: false
			}
		},
		onLoad(options) {
			console.log(options.game)
			let game = JSON.parse(options.game)
			this.gameSatrtMonth = game.gameStartMonth
			this.gameStartTime = game.gameStartTime
			this.pAName = game.pAName
			this.pAScore = game.pAScore
			this.pBName = game.pBName
			this.pBScore = game.pBScore
			this.tvChannel = game.tvChannel
			this.gameState = game.gameState
			this.gamePeriod = game.gamePeriod
			this.gameClock = game.gameClock
			this.inGame = ( this.gameState == "InProgressGame" )
			console.log(this.gameState)
		},
		methods: {

		},
		computed:{
			getTime() {
				return this.gameSatrtMonth + "·" + this.gameStartTime
			}
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
