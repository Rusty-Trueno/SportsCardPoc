<template>
	<view>
		<uni-card
			:title=sportsType
			mode="title"
			:is-shadow="true"
			:extra=sportsLeague
			note="tips"
		>
			<view>{{gameState}}</view>
			<view>{{pAName}}  vs  {{pBName}}</view>
			<view v-if="gameState == 'InProgressGame'">{{pAScore}} : {{pBScore}}</view>
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

<style>

</style>
