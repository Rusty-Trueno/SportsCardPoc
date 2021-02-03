<template>
	<view>
		<uni-card
			:title=getTitle
			:is-shadow="true"
			note="Life lies in sport."
		>
			<view class="uni-flex uni-row" style="height:140rpx;" @click="checkDetail()">
			  <view class="uni-flex uni-row" style="padding-right: 270rpx; width: 240rpx;">
				<view class="flex-item uni-column">
				  <view class="margin-0 uni-flex">
					<image src="./static/Lakers.png" style="width: 40rpx; height: 40rpx;"></image>
					<text>{{pAName}}</text>
				  </view>
				  <view class="margin-0 uni-flex" style="padding-top: 50rpx;">
					<image src="./static/Celts.png" style="width: 40rpx; height: 40rpx;"></image>
				  	<text>{{pBName}}</text>
				  </view>
				</view>
			  </view>
			  <view class="uni-flex uni-row" style=" width: 150rpx;">
				  <view class="flex-item uni-column">
					<view class="margin-0 uni-flex-sub" style="padding-left: 10rpx;">
						<text>{{getStartMonth}}</text>
					</view>
					<view class="margin-0 uni-flex-sub">
						<text>{{getStartTime}}</text>
					</view>
					<view class="margin-0 uni-flex-sub" style="padding-left: 24rpx;">
						<text>{{tvChannel}}</text>
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
		name: "SportsCard",
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
				months: ["Jan","Feb","Mar","Apr","May","Jun","Jul","Aug","Spt","Oct","Nov","Dec"]
			}
		},
		computed: {
			getStartMonth () {
				let time = new Date(parseInt(this.gameStartTime))
				return this.months[time.getMonth()+1] + " " + time.getDate()
			},
			getStartTime () {
				let time = new Date(parseInt(this.gameStartTime))
				return ( time.getHours() >= 12 ? time.getHours()-12 : time.getHours() )+ ":" +
				( time.getMinutes() > 10 ? time.getMinutes() : '0' + time.getMinutes() ) + " " +
				( time.getHours() >= 12 ? "PM" : "AM")
			},
			getTitle () {
				let time = new Date(parseInt(this.gameStartTime))
				return "Week " + this.getYearWeek(time.getFullYear(), time.getMonth()+1, time.getDate())
			}
		},
		methods: {
			getYearWeek(year,month,date){
				let dateNow = new Date(year, parseInt(month) - 1, date);
				let dateFirst = new Date(year, 0, 1);
				let dataNumber = Math.round((dateNow.valueOf() - dateFirst.valueOf()) / 86400000);
				return Math.ceil((dataNumber + ((dateFirst.getDay() + 1) - 1)) / 7);
			},
			checkDetail () {
				console.log(this.sportsLeague)
				let game = {
					"pAName": this.pAName,
					"pAScore": this.pAScore,
					"pBName": this.pBName,
					"pBScore": this.pBScore,
					"gameStartMonth": this.getStartMonth,
					"gameStartTime": this.getStartTime,
					"tvChannel": this.tvChannel,
					"gameClock": this.gameClock,
					"gameState": this.gameState,
					"gamePeriod": this.gamePeriod
				}
				uni.navigateTo({
					url:'./SportsDetail?game=' + JSON.stringify(game)
				})
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
