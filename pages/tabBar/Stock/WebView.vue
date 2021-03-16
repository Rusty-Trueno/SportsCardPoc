<template>
	<view>
	  <web-view :src="url"></web-view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				 url: 'http://www.baidu.com'
			}
		},
		onLoad(op) {
			this.getWebviewPage(op.id,op.type);
		},
		methods: {
			getWebviewPage(id,type){
				//设置 webview 界面的状态栏的 title
				if(type === '1'){
					uni.setNavigationBarTitle({
						title: 'XXXXXX'
					});
				}
			}
		}
	}
</script>

<style scoped>
.webview {
	width: 100vw;
	height: 100vh;
}
</style>
