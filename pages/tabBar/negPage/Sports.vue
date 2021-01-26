<template>
	<view class="uni-container">
		<uni-list v-for="(sport, index) in sports" :key="index">
			<text style="color: #07C160;">{{sport.sportsLeague}}</text>
			<uni-list v-for="(game, index) in sport.sportsMatches" :key="index" >
				<sports-card
					:sportsType=game.sportsType
					:sportsLeague=game.sportsLeague
					:gameClock=game.gameClock
					:gamePeriod=game.gamePeriod
					:gameStartTime=game.gameStartDateTime
					:gameState=game.gameState
					:pAName=game.pAName
					:pAScore=game.pAScore
					:pBName=game.pBName
					:pBScore=game.pBScore
					:tvChannel=game.tvChannel
					mode="title"
				/>
			</uni-list>
		</uni-list>
	</view>
</template>

<script>
	import sportsCard from './SportsCard.vue'
	var timerUpId,timerDownId;
	export default {
		components: {sportsCard},
		data() {
			return {
				sports: [],
			}
		},
		onShow() {
			this.getSportsInfo()
		},
		/***部分代码****/
		onPullDownRefresh() { //触发上拉刷新函数
			this.getSportsInfo()
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
		methods: {
			getSportsInfo() {
				this.sports = []
				uni.request({
					url: "http://localhost:26216/PeoplePredictionsB2/graphql",
					dataType: 'json',
					method: 'POST',
					data: {"operationName":"mainFeed","variables":{},"query":"query mainFeed {\n  feed {\n    main: section(input: {top: 10, section: \"DynamicFeed\"}) {\n      ...feedSection\n      __typename\n    }\n    __typename\n  }\n}\n\nfragment feedSection on FeedSingleSectionType {\n  requestId\n  items {\n    ...feedItem\n    __typename\n  }\n  __typename\n}\n\nfragment feedItem on FeedItemInterface {\n  ...compositeFeedItem\n  __typename\n}\n\nfragment sportsRecommendationItem on SportsRecommendationFeedItemType {\n  sportsRecommendationInfo {\n    sportsType\n    sportsLeague\n    sportsMatches {\n      gameId\n      gameClock\n      gameState\n      gamePeriod\n      gameStartDateTime\n      sportsType\n      sportsLeague\n      tvChannel\n      pAName\n      pAScore\n      pAImageId\n      pBName\n      pBScore\n      pBImageId\n      __typename\n    }\n    __typename\n  }\n  __typename\n}\n\nfragment compositeFeedItem on CompositeFeedItemType {\n  reason\n  composition {\n    ...sportsRecommendationItem\n    __typename\n  }\n  __typename\n}\n"},
					success: (res) => {
						let results = res.data.data.feed.main.items.map(function(val, index){
							if (val.composition != null && val.composition[0].__typename == "SportsRecommendationFeedItemType") {
								return val.composition
							}
						})
						for (let i in results) {
							if (results[i] != null) {
								let sports = results[i]
								for (let j in sports) {
									this.sports.push(sports[j].sportsRecommendationInfo)
								}
							}
						}
					}
				})
			}
		}
	}
</script>

<style>

</style>
