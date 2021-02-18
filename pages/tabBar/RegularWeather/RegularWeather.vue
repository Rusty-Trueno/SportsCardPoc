<template>
	<view>
		<uni-card
			title="Weather"
			mode="title"
			:is-shadow="true"
		>
			<view class="uni-flex uni-row" style="height: 300rpx;">
				<view class="margin-0 flex-item" style="font-size: large;">
					<image :src='curlIconUrl' style="width: 100rpx; height: 100rpx;"></image>
				</view>
				<view class="margin-0 flex-item uni-column" style="font-size: large; padding-left: 10rpx; padding-top: 20rpx;">
					<view class="margin-0 flex-item uni-row">
						<view class="margin-0 flex-item" style="font-size: 50rpx; color: #000000; font-weight: 800;">
							{{temperature()}}
						</view>
					</view>
					<view class="margin-0 flex-item" style="font-size: 10rpx; color: #000000; font-weight: 500;">
						{{curUvDesc}} today
					</view>
					<view class="margin-0 flex-item" style="font-size: 10rpx; color: #000000; font-weight: 500;">
						{{curRH}}%
					</view>
				</view>
				<view class="margin-0 flex-item uni-column" style="font-size: large; padding-top: 20rpx; padding-right: 20rpx;">
					<view class="margin-0 flex-item" style="font-size: 10rpx; color: #000000;" :class="FWeight" v-on:click="changeUnit(true)">
						F°
					</view>
					<hr/>
					<view class="margin-0 flex-item" style="font-size: 10rpx; color: #000000;" :class="CWeight" v-on:click="changeUnit(false)">
						C°
					</view>
				</view>
				<view class="margin-0 flex-item" style="font-size: large; width: 500rpx;">
					<view class="uni-padding-wrap">
						<view class="page-section swiper">
							<view class="page-section-spacing">
								<swiper class="swiper"
								:indicator-dots="indicatorDots"
								:autoplay="autoplay"
								:interval="interval"
								:duration="duration"
								:display-multiple-items="4">
									<swiper-item v-for="(weather, index) in dailyWeathers" :key="index">
										<view class="swiper-item uni-column">
											<view class="margin-0 flex-item" style="font-size: 10rpx; color: #000000; font-weight: 100; text-align: left;">
												{{day(weather.vaildDate)}}
											</view>
											<view class="margin-0 flex-item" style="padding-top: 20rpx;">
												<image :src='weather.iconUrl' style="width: 50rpx; height: 50rpx;"></image>
											</view>
											<view class="margin-0 flex-item" style="font-size: 10rpx; color: #000000; font-weight: 800;">
												{{weather.tempHi}}°
											</view>
											<view class="margin-0 flex-item" style="font-size: 10rpx; color: #000000; font-weight: 100;">
												{{weather.tempLo}}°
											</view>
										</view>
									</swiper-item>
								</swiper>
							</view>
						</view>
					</view>
				</view>
			</view>
		</uni-card>
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
				curlIconUrl: '',
				curTemp: '',
				curRH: '',
				curUvDesc: '',
				dailyWeathers: [],
				indicatorDots: true,
				autoplay: false,
				interval: 2000,
				duration: 500,
				isF: true,
				FWeight: 'font-weight: 800',
				CWeight: 'font-weight: 100'
			}
		},
		onShow() {
			this.getRegularWeather()
		},
		/***部分代码****/
		onPullDownRefresh() { //触发上拉刷新函数
			this.getRegularWeather()
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
			day(time){
				return function(time) {
				   var givenDate = new Date(time);
				   var now = new Date();
				   if (now.getDate() == givenDate.getDate()) return 'Today';
				   var day = givenDate.getDay();
				   var weeks = new Array("Sun", "Mon", "Tue", "Wen", "Tur", "Fri", "Sat");
				   var week = weeks[day];
				   return week;
				}
			},
			temperature(){
				return function() {
					if (this.isF) {
						return this.curTemp
					} else {
						return String(parseInt((Number(this.curTemp)-32)/1.8))
					}
				}
			}
		},
		methods: {
			getRegularWeather() {
				uni.request({
					url: "http://localhost:26216/PeoplePredictionsB2/graphql",
					dataType: 'json',
					method: 'POST',
					data: {"operationName":"mainFeed","variables":{},"query":"query mainFeed {\n  feed {\n    main: section(input: {top: 10, section: \"DynamicFeed\", latitude: \"35.88334\", longitude: \"131\"}) {\n      ...feedSection\n      __typename\n    }\n    __typename\n  }\n}\n\nfragment feedSection on FeedSingleSectionType {\n  requestId\n  items {\n    ...feedItem\n    __typename\n  }\n  __typename\n}\n\nfragment feedItem on FeedItemInterface {\n  ...regularWeatherItem\n  __typename\n}\n\nfragment regularWeatherItem on RegularWeatherFeedItemType {\n  regularWeatherInfo {\n    iconUrl\n    temp\n    rh\n    uvDesc\n    dailyWeathers {\n      ...dailyWeather\n    }\n  }\n}\n\nfragment dailyWeather on DailyWeatherType {\n  iconUrl\n  vaildDate\n  tempHi\n  tempLo\n}\n"},
					success: (res) => {
						for (let i in res.data.data.feed.main.items) {
							let item = res.data.data.feed.main.items[i];
							if (item.__typename == "RegularWeatherFeedItemType") {
								this.curlIconUrl = item.regularWeatherInfo.iconUrl
								this.curTemp = item.regularWeatherInfo.temp
								this.curRH = item.regularWeatherInfo.rh
								this.curUvDesc = item.regularWeatherInfo.uvDesc
								this.dailyWeathers = item.regularWeatherInfo.dailyWeathers
								console.log(this.dailyWeathers)
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
			},
			changeUnit(isF) {
				this.isF = isF
				if (isF) {
					this.FWeight = 'font-weight: 800'
					this.CWeight = 'font-weight: 100'
				} else {
					this.FWeight = 'font-weight: 100'
					this.CWeight = 'font-weight: 800'
				}
			},
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
