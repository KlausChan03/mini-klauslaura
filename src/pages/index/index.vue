<template>
	<view>
			<home v-if="PageCur == 'home'"></home>
			<myself v-if="PageCur == 'myself'"></myself>
			<view class="cu-bar tabbar bg-white shadow foot">
				<view class="action" @click="NavChange" data-cur="home">
					<iconfont name="lalaksks-chuangzuozhongxin" size="16" :color="PageCur == 'home' ? '#48e' : '#bbb'" ></iconfont>
					<view :class="[PageCur == 'home' ? 'col-48e' : 'col-bbb', 'mt-5', 'fs-15']"
						>首页</view
					>
				</view>
				<view class="action" @click="NavChange" data-cur="myself">
					<iconfont name="lalaksks-windows" size="16" :color="PageCur == 'myself' ? '#48e' : '#bbb'" ></iconfont>
					<view :class="[PageCur == 'myself' ? 'col-48e' : 'col-bbb', 'mt-5', 'fs-15']"
						>我的</view
					>
				</view>
			</view>		
	</view>
</template>

<script>
import iconfont from '@/components/iconfont/iconfont'
export default {
	data() {
		return {
			PageCur: 'home',
		}
	},
	components: {
		iconfont
	},
	methods: {
		NavChange: function(e) {
			this.PageCur = e.currentTarget.dataset.cur
		},
	},
}
</script>

<style></style>
