<template name="home">
	<view class="page home-page">
		<cu-custom bgColor="#0081FF">
			<block slot="content" style="color:#FFFFFF">首页</block>
		</cu-custom>
		<view class="nav text-center bg-mini">
			<scroll-view
				class="nav-header flex-ha-vc bg-blue"
				:show-scrollbar="false"
				:scroll-into-view="scrollInto"
			>
				<view
					class="cu-item flex-1-2"
					:class="0 == currentTab ? 'text-white cur flex-hc-vc' : ''"
					@tap="tabSelect"
					data-id="0"
				>
					<view class="flex-hc-vc">
						<text class="cuIcon-creative mr-10 fs-22"></text>
						<text class="fs-16">文章</text>
					</view>
				</view>
				<view
					class="cu-item flex-1-2"
					:class="1 == currentTab ? 'text-white cur flex-hc-vc' : ''"
					@tap="tabSelect"
					data-id="1"
				>
					<view class="flex-hc-vc">
						<text class="cuIcon-mark mr-10 fs-22"></text>
						<text class="fs-16">瞬间</text>
					</view>
				</view>
			</scroll-view>

			<scroll-view
				class="nav-main"
				scroll-y
				enable-back-to-top
				@scrolltolower="loadMore()"
			>
				<view v-if="0 == currentTab && ifShowContent" class="text-center">
					<view						
						class="cu-card article"
						:class="isCard ? 'no-card' : ''"
					>
						<view class="cu-item shadow p-0 flex-hl-vc" >
							你好,{{ isLogin ? userInfo.nickName : '游客'}}
						</view>
						<view v-for="(item, index) in listOfArticle" :key="index" class="cu-item shadow" @click="goDetail(item)">
							<view class="title">
								<view class="text-cut col-333">{{ item.title.rendered }}</view>
							</view>
							<view class="content">
								<view class="desc">
									<view
										class="text-content col-555"
										v-html="item.excerpt.rendered"
										:id="item.id"
									>
									</view>
									<view class="flex-hc-vc fs-12 col-777" v-if="item.post_metas.address">
										<text class="cuIcon-location mr-5"></text>
										{{ item.post_metas.address }}
									</view>
									<view class="flex-hb-vc flex-hw mt-10">
										<view>
											<view class="fs-14">{{ item.date | formateDate }}</view>
										</view>
										<view>
											<view
												class="cu-tag bg-red light round mr-5"
												v-if="item.sticky"
												>top</view
											>
											<view
												class="cu-tag bg-green light round mr-5"
												v-if="item.newest"
												>new</view
											>
											<view
												class="cu-tag bg-blue light round mr-5"
												v-if="item.hotest"
												>hot</view
											>
											<template
												v-if="item.post_metas && item.post_metas.cat_name"
											>
												<view
													class="cu-tag bg-mauve light round mr-5"
													v-for="(sonItem, sonIndex) in item.post_metas
														.cat_name"
													:key="sonIndex"
													>{{ sonItem }}</view
												>
											</template>
											<template
												v-if="item.post_metas && item.post_metas.tag_name"
											>
												<view
													class="cu-tag bg-cyan light round mr-5"
													v-for="(sonItem, sonIndex) in item.post_metas
														.tag_name"
													:key="sonIndex"
													>{{ sonItem }}</view
												>
											</template>
										</view>
									</view>
								</view>
							</view>
						</view>
					</view>
					<view
						class="cu-load bg-blue"
						:class="!isLoad ? 'loading' : 'over'"
					></view>
				</view>
				<view v-if="1 == currentTab && ifShowContent" class="text-center">
					<view
						v-for="(item, index) in listOfChat"
						:key="index"
						class="cu-card article"
						:class="isCard ? 'no-card' : ''"
					>
						<view class="cu-item shadow">
							<view class="title" v-if="item.title.rendered">
								<view class="text-cut col-333">#{{ item.title.rendered }}#</view>
							</view>
							<view class="content">
								<view class="desc col-555">
									<view
										class="text-content"
										v-html="item.content.rendered"
										:id="item.id"
									>
									</view>
									<view class="flex-hc-vc fs-12 col-777" v-if="item.post_metas.address">
										<text class="cuIcon-location mr-5"></text>
										{{ item.post_metas.address }}
									</view>
									<view class="flex-hb-vc flex-hw mt-10">
										<view>
											<view class="fs-14">{{ item.date | formateDate }}</view>
										</view>
										<view>
											<view
												class="cu-tag bg-red light round mr-5"
												v-if="item.sticky"
												>top</view
											>
											<view
												class="cu-tag bg-green light round mr-5"
												v-if="item.newest"
												>new</view
											>
											<view
												class="cu-tag bg-blue light round mr-5"
												v-if="item.hotest"
												>hot</view
											>
											<template
												v-if="item.post_metas && item.post_metas.cat_name"
											>
												<view
													class="cu-tag bg-mauve light round mr-5"
													v-for="(sonItem, sonIndex) in item.post_metas
														.cat_name"
													:key="sonIndex"
													>{{ sonItem }}</view
												>
											</template>
											<template
												v-if="item.post_metas && item.post_metas.tag_name"
											>
												<view
													class="cu-tag bg-cyan light round mr-5"
													v-for="(sonItem, sonIndex) in item.post_metas
														.tag_name"
													:key="sonIndex"
													>{{ sonItem }}</view
												>
											</template>
										</view>
									</view>
								</view>
							</view>
						</view>
					</view>
					<view
						class="cu-load bg-blue"
						:class="!isLoad ? 'loading' : 'over'"
					></view>
				</view>
			</scroll-view>
		</view>
		<view class="cu-load load-modal col-48e" v-if="!ifShowContent">
			<view class="gray-text">Loading...</view>
		</view>
	</view>
</template>
<script>
import dayjs from 'dayjs'
require('dayjs/locale/zh-cn')
var relativeTime = require('dayjs/plugin/relativeTime')
dayjs.locale('zh-cn')
dayjs.extend(relativeTime)

export default {
	data() {
		return {
			site_url: 'https://klauslaura.cn',
			isLogin: false,
			userInfo: {},
			listOfChat: [],
			listOfArticle: [],
			listOfmovie: [],
			ifShowContent: false,
			totalOfChat: '',
			page: 1,
			per_page: 5,
			orderby: 'date',
			currentTab: 0,
			scrollLeft: 0,
			scrollInto: '',
			navigateFlag: false
		}
	},
	async mounted() {
		const self = this
		await uni.getStorage({
			key: 'isLogin',
			success: (res) => {
				self.isLogin = res.data
			},
		})
		await uni.getStorage({
			key: 'userInfo',
			success: (res) => {
				self.userInfo = res.data
			},
		})
		this.getAllArticle()
		
	},

	filters: {
		formateDate: (value) => {
			return dayjs(value).fromNow()
		},
	},
	methods: {
		goDetail(e) {
			if (this.navigateFlag) {
				return
			}
			const url = `../home/detail?id=${e.id}`
			this.navigateFlag = true
			if (url) {
				uni.navigateTo({
					url: url,
				})
			}		
			setTimeout(() => {
				this.navigateFlag = false
			}, 200)
		},
		tabSelect(e) {
			this.currentTab = e.currentTarget.dataset.id
			this.scrollLeft = (e.currentTarget.dataset.id - 1) * 60
			this.page = 1
			if (Number(this.currentTab) === 0) {
				this.listOfArticle = []
				this.getAllArticle()
			} else if (Number(this.currentTab) === 1) {
				this.listOfChat = []
				this.getAllChat()
			}
		},

		loadMore(e) {
			let self = this
			setTimeout(() => {
				self.page++
				if (Number(self.currentTab) === 0) {
					self.getAllArticle()
				} else if (Number(self.currentTab) === 1) {
					self.getAllChat()
				}
			}, 500)
		},

		getAllArticle() {
			if (this.page === 1) {
				this.ifShowContent = false
			}
			let params = {}
			params.page = this.page
			params.per_page = this.per_page
			params.orderby = this.orderby
			wx.request({
				url: `${this.site_url}/wp-json/wp/v2/posts`,
				data: params,
				success: (res) => {
					if (this.page === 1) {
						this.ifShowContent = true
					}
					// this.totalOfChat = parseInt(res.headers['x-wp-total'])
					this.listOfArticle = this.listOfArticle.concat(res.data)

					this.listOfArticle.forEach((element) => {
						element.ifShowComment = false
						if (
							element.date &&
							Number(dayjs(new Date()).diff(dayjs(element.date), 'week')) === 0
						) {
							element.newest = true
						}
						if (
							element.post_metas.comments_num >= 10 ||
							element.post_metas.zan_num >= 10
						) {
							element.hotest = true
						}
						if (element.excerpt) {
							element.excerpt.rendered = element.excerpt.rendered
								.replace(/<p([\s\w"=\/\.:;]+)((?:(style="[^"]+")))/gi, '<p')
								.replace(/<p([\s\w"=\/\.:;]+)((?:(class="[^"]+")))/gi, '<p')
								.replace(/<p>/gi, '<p class="p_class col-555">')
								.replace(/flex-hb-vc/gi, 'flex')
								.replace(/<img/gi, '<img class="img_class"')
						}
					})
				},
			})
		},
		getAllChat() {
			if (this.page === 1) {
				this.ifShowContent = false
			}
			let params = {}
			params.page = this.page
			params.per_page = this.per_page
			params.orderby = this.orderby
			wx.request({
				url: `${this.site_url}/wp-json/wp/v2/moments`,
				data: params,
				success: (res) => {
					if (this.page === 1) {
						this.ifShowContent = true
					}
					// this.totalOfChat = parseInt(res.headers['x-wp-total'])
					this.listOfChat = this.listOfChat.concat(res.data)
					this.listOfChat.forEach((element) => {
						element.ifShowComment = false
						if (
							element.date &&
							Number(dayjs(new Date()).diff(dayjs(element.date), 'week')) === 0
						) {
							element.newest = true
						}
						if (
							element.post_metas.comments_num >= 10 ||
							element.post_metas.zan_num >= 10
						) {
							element.hotest = true
						}
						if (element.content) {
							element.content.rendered = element.content.rendered
								.replace(/<p([\s\w"=\/\.:;]+)((?:(style="[^"]+")))/gi, '<p')
								.replace(/<p([\s\w"=\/\.:;]+)((?:(class="[^"]+")))/gi, '<p')
								.replace(/<p>/gi, '<p class="p_class">')
								// .replace(/<img/gi, '<img style="max-width:100%; object-fit:cover"')
								.replace(/<img/gi, '<img class="img_class"')
								.replace(/flex-hb-vc/gi, 'flex')
						}
					})
				},
			})
		},
	},
}
</script>

<style lang="less">
.bg-mini {
	background-color: #f0f0f0;
}

.home-page {
	flex: 1;
	flex-direction: column;
	overflow: hidden;
	background-color: #ecebeb;
	/* #ifdef MP-ALIPAY || MP-BAIDU */
	height: 100vh;
	/* #endif */
	height: 100%;
	padding-bottom: 100rpx;

	.nav {
		padding-bottom: 50rpx;

		.nav-header {
			.cu-item {
				padding: 0;
				margin: 0;
				// width: 33%;
			}
		}

		.nav-main {
			height: calc(100vh - 170px);
			// height: 100vh;
		}

		.cu-load {
			margin: 30rpx;
			margin-bottom: 0;
		}

		.cu-card {
			> .cu-item {
				height: auto;
				margin-bottom: 0;
				margin-left: 30rpx;
				margin-right: 30rpx;
				.title {
					view {
						font-size: 16px;
					}
				}
				.content {
					.text-content {
						height: auto;

						.p_class {
							line-height: 2;
							margin-top: 10px;
							max-width: 80vw;
							text-align: left;
							overflow: hidden;
							text-overflow: ellipsis;
							display: -webkit-box;
							-webkit-line-clamp: 2;
							-webkit-box-orient: vertical;
						}

						.moment-gallery {
							display: flex;

							> .p_class {
								width: 32%;
								margin-top: 5px;
								// flex: 1 0 auto;
								height: calc(100vw / 3 - 45px);

								&:nth-last-child(1):first-child {
									height: 100%;
									width: 100%;
								}

								&:not(:nth-child(3n)) {
									margin-right: 2%;
								}

								.img_class {
									width: 100%;
									height: auto;
									object-fit: cover;
								}
							}
						}
					}
				}
			}
		}
	}
}
</style>
