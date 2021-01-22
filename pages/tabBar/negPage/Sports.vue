<template>
	<view class="uni-container">
		<uni-list v-for="(game, index) in sportsMatches" :key="index">
			<uni-card
				title="Dcloud"
				mode="title"
				:is-shadow="true"
				thumbnail="https://img-cdn-qiniu.dcloud.net.cn/uniapp/images/muwu.jpg"
				extra="技术没有上限"
				note="Tips"
			>
				那是一个秋意盎然、金风送爽的日子,我和父母一起来到了位于上师大旁的康健园.一踏进公园,一股浓郁的桂香扑鼻而来,泌人心脾,让我心旷神怡,只见一朵朵开得正烈的金色桂花,迎风而立,仿佛在向我招手.我们追着这桂香,走进了清幽的公园.
			</uni-card>
		</uni-list>
	</view>
</template>

<script>
	import uniCard from '@/components/uni-card/uni-card.vue'
	export default {
		components: {uniCard},
		data() {
			return {
				sportsMatches: [],
			}
		},
		onShow() {
			this.getSportsInfo()
		},
		onPullDownRefresh() {
		  this.getSportsInfo()
		},
		methods: {
			getSportsInfo() {
				this.sportsMatches = []
				uni.request({
					url: "http://localhost:26216/PeoplePredictionsB2/graphql",
					dataType: 'json',
					method: 'POST',
					data: {"operationName":"mainFeed","variables":{},"query":"query mainFeed {\n  feed {\n    main: section(input: {top: 10, section: \"DynamicFeed\"}) {\n      ...feedSection\n      __typename\n    }\n    __typename\n  }\n}\n\nfragment feedSection on FeedSingleSectionType {\n  requestId\n  items {\n    ...feedItem\n    __typename\n  }\n  __typename\n}\n\nfragment feedItem on FeedItemInterface {\n  ...sportsRecommendationItem\n  __typename\n}\n\nfragment sportsRecommendationItem on SportsRecommendationFeedItemType {\n  sportsRecommendationInfo {\n    sportsMatches {\n      gameId\n      gameClock\n      gameState\n      gamePeriod\n      gameStartDateTime\n      sportsType\n      sportsLeague\n      tvChannel\n      pAName\n      pAScore\n      pBName\n      pBScore\n      __typename\n    }\n    __typename\n  }\n  __typename\n}\n"},
					success: (res) => {
						let results = res.data.data.feed.main.items.map(function(val, index){
							if (val.__typename == "SportsRecommendationFeedItemType") {
								return val.sportsRecommendationInfo.sportsMatches
							}
						})
						for (let i in results) {
							if (results[i] != null) {
								let games = results[i]
								for (let j in games) {
									if (games[j] != null) {
										this.sportsMatches.push(games[j])
									}
								}
							}
						}
					}
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

	.uni-border {
		position: relative;
		/* #ifdef APP-NVUE */
		border-color: #e5e5e5;
		border-style: solid;
		border-width: 0.5px;
		/* #endif */
		z-index: 1;
	}

	/* #ifndef APP-NVUE */
	.uni-border:after {
		content: '';
		position: absolute;
		bottom: 0;
		left: 0;
		top: 0;
		right: 0;
		border: 1px solid #e5e5e5;
		border-radius: 10px;
		box-sizing: border-box;
		width: 200%;
		height: 200%;
		transform: scale(0.5);
		transform-origin: left top;
		z-index: -1;
	}

	/* #endif */
	.uni-border-bottom {
		position: relative;
		/* #ifdef APP-NVUE */
		border-bottom-color: #e5e5e5;
		border-bottom-style: solid;
		border-bottom-width: 0.5px;
		/* #endif */
		z-index: 1;
	}

	/* #ifndef APP-NVUE */
	.uni-border-bottom:after {
		content: '';
		position: absolute;
		bottom: 0;
		left: 0;
		top: 0;
		right: 0;
		border-bottom: 1px solid #e5e5e5;
		box-sizing: border-box;
		width: 200%;
		height: 200%;
		transform: scale(0.5);
		transform-origin: left top;
		z-index: -1;
	}

	/* #endif */
	.uni-border-top {
		position: relative;
		/* #ifdef APP-NVUE */
		border-top-color: #e5e5e5;
		border-top-style: solid;
		border-top-width: 0.5px;
		/* #endif */
		z-index: 1;
	}

	/* #ifndef APP-NVUE */
	.uni-border-top:after {
		content: '';
		position: absolute;
		bottom: 0;
		left: 0;
		top: 0;
		right: 0;
		border-top: 1px solid #e5e5e5;
		box-sizing: border-box;
		width: 200%;
		height: 200%;
		transform: scale(0.5);
		transform-origin: left top;
		z-index: -1;
	}

	/* #endif */
	.uni-card__thumbnailimage {
		position: relative;
		flex-direction: column;
		justify-content: center;
		height: 150px;
		overflow: hidden;
	}

	.uni-card__thumbnailimage-box {
		/* #ifndef APP-NVUE */
		display: flex;
		/* #endif */
		flex: 1;
		flex-direction: row;
		overflow: hidden;
	}

	.uni-card__thumbnailimage-image {
		flex: 1;
	}

	.uni-card__thumbnailimage-title {
		/* #ifndef APP-NVUE */
		display: flex;
		/* #endif */
		position: absolute;
		bottom: 0;
		left: 0;
		right: 0;
		flex-direction: row;
		padding: 8px 12px;
		background-color: rgba(0, 0, 0, 0.4);
	}

	.uni-card__thumbnailimage-title-text {
		flex: 1;
		font-size: 14px;
		color: #fff;
	}

	.uni-card__title {
		/* #ifndef APP-NVUE */
		display: flex;
		/* #endif */
		flex-direction: row;
		align-items: center;
		padding: 10px;
	}

	.uni-card__title-box {
		/* #ifndef APP-NVUE */
		display: flex;
		/* #endif */
		flex: 1;
		flex-direction: row;
		align-items: center;
		overflow: hidden;
	}

	.uni-card__title-header {
		width: 40px;
		height: 40px;
		overflow: hidden;
		border-radius: 5px;
	}

	.uni-card__title-header-image {
		width: 40px;
		height: 40px;
	}

	.uni-card__title-content {
		/* #ifndef APP-NVUE */
		display: flex;
		/* #endif */
		flex-direction: column;
		justify-content: center;
		flex: 1;
		padding-left: 10px;
		height: 40px;
		overflow: hidden;
	}

	.uni-card__title-content-title {
		font-size: 14px;
		line-height: 22px;
	}

	.uni-card__title-content-extra {
		font-size: 12px;
		line-height: 27px;
		color: #999;
	}

	.uni-card__header {
		/* #ifndef APP-NVUE */
		display: flex;
		/* #endif */
		position: relative;
		flex-direction: row;
		padding: 12px;
		align-items: center;
	}

	.uni-card__header-title {
		/* #ifndef APP-NVUE */
		display: flex;
		/* #endif */
		flex-direction: row;
		margin-right: 8px;
		justify-content: flex-start;
		align-items: center;
	}

	.uni-card__header-title-text {
		font-size: 16px;
		flex: 1;
		color: #333;
	}

	.uni-card__header-extra-img {
		height: 20px;
		width: 20px;
		margin-right: 8px;
	}

	.uni-card__header-extra-text {
		flex: 1;
		margin-left: 8px;
		font-size: 12px;
		text-align: right;
		color: #999;
	}

	.uni-card__content {
		color: #333;
	}

	.uni-card__content--pd {
		padding: 12px;
	}

	.uni-card__content-extra {
		font-size: 14px;
		padding-bottom: 10px;
		color: #999;
	}

	.uni-card__footer {
		justify-content: space-between;
		padding: 12px;
	}

	.uni-card__footer-text {
		color: #999;
		font-size: 12px;
	}

	.uni-card--shadow {
		position: relative;
		/* #ifndef APP-NVUE */
		box-shadow: 0px 0px 5px 1px rgba(0, 0, 0, 0.1);
		/* #endif */
	}

	.uni-card--full {
		margin: 0;
		border-radius: 0;
	}

	/* #ifndef APP-NVUE */
	.uni-card--full:after {
		border-radius: 0;
	}

	/* #endif */
	.uni-ellipsis {
		/* #ifndef APP-NVUE */
		overflow: hidden;
		white-space: nowrap;
		text-overflow: ellipsis;
		/* #endif */
		/* #ifdef APP-NVUE */
		lines: 1;
		/* #endif */
	}
</style>
