<template>
	<view class="uni-container">
		<uni-list>
			<uni-list-item title="列表文字" rightText="右侧文字" />
			<uni-list-item title="列表文字" note="列表描述信息" rightText="右侧文字" />
		</uni-list>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				sportsMatches: []
			}
		},
		onLoad() {
			this.getSportsInfo()
		},
		methods: {
			getSportsInfo() {
				uni.request({
					url: "http://localhost:26216/PeoplePredictionsB2/graphql",
					dataType: 'json',
					method: 'POST',
					data: {"operationName":"mainFeed","variables":{},"query":"query mainFeed {\n  feed {\n    main: section(input: {top: 10, section: \"DynamicFeed\"}) {\n      ...feedSection\n      __typename\n    }\n    __typename\n  }\n}\n\nfragment feedSection on FeedSingleSectionType {\n  requestId\n  items {\n    ...feedItem\n    __typename\n  }\n  __typename\n}\n\nfragment feedItem on FeedItemInterface {\n  ...sportsRecommendationItem\n  __typename\n}\n\nfragment sportsRecommendationItem on SportsRecommendationFeedItemType {\n  sportsRecommendationInfo {\n    sportsMatches {\n      gameId\n      gameClock\n      gameState\n      gamePeriod\n      gameStartDateTime\n      sportsType\n      sportsLeague\n      tvChannel\n      pAName\n      pAScore\n      pBName\n      pBScore\n      __typename\n    }\n    __typename\n  }\n  __typename\n}\n"},
					success: (res) => {
						console.log(res.data.data.feed.main)
						this.sportsMatches = res.data.data.feed.main.items.map(function(val, index){
							if (val.__typename == "SportsRecommendationFeedItemType") {
								return val.sportsRecommendationInfo.sportsMatches
							}
						})
						console.log(this.sportsMatches[0])
					}
				})
			}
		}
	}
</script>

<style>

</style>
