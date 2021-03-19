<template>
	<view>
		<view class="uni-flex uni-row">
			<view class="margin-0 flex-item">
				<image src='./static/Stocks.png' style="width: 50rpx; height: 50rpx; padding-right: 30rpx; padding-left: 20rpx;"></image>
			</view>
			<view class="margin-0 flex-item" style="font-size: 10rpx; font-weight: 500">
				Money · My Watchlist
			</view>
		</view>
		<uni-card
			:is-shadow="true"
		>
			<view>
				<uni-list v-for="(stock, index) in stocks" :key="index">
					<view class="uni-flex uni-row">
						<view class="uni-flex uni-column" style="height: 150rpx;">
							<view class="margin-0 flex-item" style="font-size: 10rpx; color: #000000; font-weight:900; width: 300rpx;">
								{{stock.symbol}}
							</view>
							<view class="margin-0 flex-item" style="font-size: 10rpx; color: #000000; width: 300rpx; font-weight: 100;">
								{{stock.displayName}}
							</view>
						</view>
						<view class="uni-flex uni-row">
							<view class="margin-0 flex-item" style="font-size: 10rpx; color: #000000; font-weight: bold;width: 150rpx; padding-left: 100rpx;">
								{{stock.price}}
							</view>
							<view class="margin-0 flex-item" style="font-size: 1px; color: #ffffff; width: 100rpx; height: 50rpx; text-align: center; border-radius: 8px;" :style="{ backgroundColor: backgroundColor(stock.priceChangePercent) }">
								{{getPriceChangePercent(stock.priceChangePercent)}}
							</view>
						</view>
					</view>
				</uni-list>
			</view>
		</uni-card>
		<button @click="seeMoreStocks()">
			See more
		</button>
		<form @submit="addStocks">
			<view class="uni-flex uni-row" style="padding-top: 50rpx; padding-left: 30rpx;">
				<view class="margin-0 flex-item" style="width: 500rpx;">
					<input class="uni-input" name="input" placeholder-style="color:#000000" placeholder="Please input targetId of stocks" />
				</view>
				<view class="margin-0 flex-item">
					<button form-type="submit">
						Add
					</button>
				</view>
			</view>
		</form>
	</view>
</template>

<script>
	import uniCard from '@/components/uni-card/uni-card.vue'
	var timerUpId,timerDownId;
	export default {
		components: {uniCard},
		name: "RegularWeather",
		data() {
			return {
				stocks: [],
				seeMore: false,
				url: ''
			}
		},
		onShow() {
			this.getWatchStocks()
		},
		/***部分代码****/
		onPullDownRefresh() { //触发上拉刷新函数
			this.getWatchStocks()
			if (timerUpId != null) { //为防止回弹多次触发刷新
				clearTimeout(timerUpId)
			}
			timerUpId = setTimeout(() => {
				console.log("下拉刷新");
				uni.stopPullDownRefresh(); //停止下拉刷新动画
			}, 1000)
		},
		onReachBottom() { //监听页面触底函数
			if (timerDownId != null) { //为防止回弹多次触发刷新
				clearTimeout(timerDownId)
			}
			timerDownId = setTimeout(() => {
				console.log("上拉加载");
			}, 500)

		},
		computed: {
			backgroundColor(priceChangePercent){
				return function(priceChangePercent) {
					var percent = parseFloat(priceChangePercent)
					if (percent >= 0) {
						return "#00aa00"
					} else {
						return "#aa0000"
					}
				}
			},
			getPriceChangePercent(priceChangePercent){
				return function(priceChangePercent) {
					var percent = parseFloat(priceChangePercent)
					if (percent >= 0) {
						return "+" + priceChangePercent
					} else {
						return priceChangePercent
					}
				}
			}
		},
		methods: {
			getWatchStocks() {
				this.seeMore = false
				uni.request({
					url: "http://localhost:26216/PeoplePredictionsB2/graphql",
					dataType: 'json',
					method: 'POST',
					data: {"operationName":"mainFeed","variables":{},"query":"query mainFeed {\n  feed {\n    main: section(input: {top: 10, section: \"DynamicFeed\"}) {\n      ...feedSection\n      __typename\n    }\n    __typename\n  }\n}\n\nfragment feedSection on FeedSingleSectionType {\n  requestId\n  items {\n    ...feedItem\n    __typename\n  }\n  __typename\n}\n\nfragment feedItem on FeedItemInterface {\n  ...stockItem\n  __typename\n}\n\nfragment stockItem on StockFeedItemType {\n  stockInfo {\n    stocks {\n      ...stock\n    }\n  }\n  deeplink\n  __typename\n}\n\nfragment stock on StockType {\n  price\n  priceChange\n  priceDayLow\n  priceDayHigh\n  priceDayOpen\n  priceChangePercent\n  pricePreviousClose\n  marketCap\n  market\n  shortName\n  displayName\n  symbol\n}\n"},
					success: (res) => {
						for (let i in res.data.data.feed.main.items) {
							let item = res.data.data.feed.main.items[i];
							if (item.__typename == "StockFeedItemType") {
								this.stocks = item.stockInfo.stocks
								this.url = item.deeplink
							}
						}
					}
				})
			},
			seeMoreStocks() {
				window.location.href=this.url;
			},
			addStocks: function(e) {
                console.log('Stocks added：' + JSON.stringify(e.detail.value))
                var formdata = e.detail.value
				uni.request({
					url: "http://localhost:26216/PeoplePredictionsB2/graphql",
					dataType: 'json',
					method: 'POST',
					data: {"operationName":"addStock","variables":{"input":{"targetId": e.detail.value.input}},"query":"mutation addStock($input: StockMutationInputType!) {\n  stock {\n    create(input: $input) {\n      actionInfo {\n        actionType\n        ownerId\n        targetId\n        targetType\n        deleted\n        __typename\n      }\n      __typename\n    }\n  }\n}\n"},
					success: (res) => {
						uni.showModal({
						    content: 'Stocks added：' + JSON.stringify(formdata.input),
						    showCancel: false
						});
						this.getWatchStocks()
					}
				})
            }
		}
	}
</script>

<style scoped>
	.uni-card {
		background-size: cover;
		color: #000000;
	}
	/deep/ .uni-card__footer-text {
		color: #ffffff;
	}
	/deep/ .uni-card__title-content-extra {
		color: #ffffff;
	}
</style>
