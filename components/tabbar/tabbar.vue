<template>
	<view>
		<u-tabbar :list="tabbar" :mid-button="true"></u-tabbar>
	</view>
</template>

<script>
	export default {
		name: "tabbar",
		data() {
			return {
				tabbar: [{
						"pagePath": "/pages/home/index",
						"iconPath": "/static/img/tabbar/home.png",
						"selectedIconPath": "/static/img/tabbar/homeactive.png",
						"text": "首页"
					},
					{
						"pagePath": "/pages/tabbar/tabbar-2/tabbar-2",
						"iconPath": "/static/img/tabbar/guanzhu.png",
						"selectedIconPath": "/static/img/tabbar/guanzhuactive.png",
						"text": "关注"
					},
					{
						"pagePath": "/pages/tabbar/tabbar-3/tabbar-3",
						"iconPath": "/static/img/tabbar/add.png",
						"selectedIconPath": "/static/img/tabbar/addactive.png",
						midButton: true,
						customIcon: false,
						"text": "发布"
					},
					{
						"pagePath": "/pages/tabbar/tabbar-4/tabbar-4",
						"iconPath": "/static/img/tabbar/news.png",
						"selectedIconPath": "/static/img/tabbar/newsactive.png",
						"text": "消息"
					},
					{
						"pagePath": "/pages/tabbar/tabbar-5/tabbar-5",
						"iconPath": "/static/img/tabbar/me.png",
						"selectedIconPath": "/static/img/tabbar/meactive.png",
						"text": "我"
					}
				]
			}
		},
	}
</script>

<style lang="scss">

</style>