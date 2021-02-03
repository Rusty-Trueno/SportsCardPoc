<template>
	<view>
		<uni-list v-for="(alert, index) in alerts" :key="index">
			<uni-card
				:title="alert.title"
				:subTitle="alert.severity"
				mode="title"
				:is-shadow="true"
				:note="'Reported by: ' + alert.credit"
			>
				<view class="uni-flex uni-column">
					<view class="margin-0 flex-item" style="font-size: large;"> {{ alert.event + " from " + alert.start + " to " + alert.end }} </view>
					<view class="margin-0 flex-item" style="padding-top: 50rpx; width: 400rpx;"> {{ alert.desc }} </view>
				</view>
			</uni-card>
		</uni-list>
	</view>
</template>

<script>
	import uniCard from '@/components/uni-card/uni-card.vue'
	var timerUpId,timerDownId;
	export default {
		components: {uniCard},
		name: "WeatherAlert",
		data() {
			return {
				alerts: []
			}
		},
		onShow() {
			this.getWeatherAlerts()
		},
		/***部分代码****/
		onPullDownRefresh() { //触发上拉刷新函数
			this.getWeatherAlerts()
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
			getWeatherAlerts() {
				this.alerts = []
				uni.request({
					url: "http://localhost:26216/PeoplePredictionsB2/graphql",
					dataType: 'json',
					method: 'POST',
					data: {"operationName":"mainFeed","variables":{},"query":"query mainFeed {\n  feed {\n    main: section(input: {top: 10, section: \"DynamicFeed\", latitude: \"35.88334\", longitude: \"131\"}) {\n      ...feedSection\n      __typename\n    }\n    __typename\n  }\n}\n\nfragment feedSection on FeedSingleSectionType {\n  requestId\n  items {\n    ...feedItem\n    __typename\n  }\n  __typename\n}\n\nfragment feedItem on FeedItemInterface {\n  ...weatherAlertItem\n  __typename\n}\n\nfragment weatherAlertItem on WeatherAlertFeedItemType {\n  weatherAlertInfo {\n    ...weatherAlertFacet\n  }\n}\n\nfragment weatherAlertFacet on WeatherAlertType {\n  title\n  severity\n  desc\n  credit\n  event\n  start\n  end\n}\n"},
					success: (res) => {
						let results = res.data.data.feed.main.items.map(function(val, index){
							if (val.__typename == "WeatherAlertFeedItemType") {
								return val.weatherAlertInfo
							}
						})
						//console.log(results)
						for (let i in results) {
							if (results[i] != null) {
								let alerts = results[i];
								for (let j in alerts) {
									alerts[j].start = this.dateFormat("YYYY-mm-dd HH:MM", new Date(alerts[j].start))
									alerts[j].end = this.dateFormat("YYYY-mm-dd HH:MM", new Date(alerts[j].end))
									this.alerts.push(alerts[j])
								}
							}
						}
					}
				})
			},
			dateFormat(fmt, date) {
			    let ret;
			    const opt = {
			        "Y+": date.getFullYear().toString(),        // 年
			        "m+": (date.getMonth() + 1).toString(),     // 月
			        "d+": date.getDate().toString(),            // 日
			        "H+": date.getHours().toString(),           // 时
			        "M+": date.getMinutes().toString(),         // 分
			        "S+": date.getSeconds().toString()          // 秒
			        // 有其他格式化字符需求可以继续添加，必须转化成字符串
			    };
			    for (let k in opt) {
			        ret = new RegExp("(" + k + ")").exec(fmt);
			        if (ret) {
			            fmt = fmt.replace(ret[1], (ret[1].length == 1) ? (opt[k]) : (opt[k].padStart(ret[1].length, "0")))
			        };
			    };
			    return fmt;
			}
		}
	}
</script>

<style scoped>
	.uni-card {
		background-image: url(static/background.jpg);
		background-size: cover;
		font-weight: bold;
		color: #ffffff;
	}
	/deep/ .uni-card__footer-text {
		color: #ffffff;
	}
	/deep/ .uni-card__title-content-extra {
		color: #ffffff;
	}
</style>
