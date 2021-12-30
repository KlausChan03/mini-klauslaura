<template>
	<view>
		<cu-custom bgColor="bg-gradual-pink" :isBack="true">
			<block slot="backText">返回</block>
			<block slot="content">我的信息</block>
		</cu-custom>
		<view class="info-main cu-card" v-if="ifShowContent">
			<view class="cu-item" v-for="item in info" :key="item.name">
				<template slot="title">
					<h3>{{ item.name }}</h3>
				</template>
				<template v-if="item.value && item.value.length > 0">
					<template v-if="item.type === 'simple'">
						<template v-for="(sonItem, sonIndex) in item.value">
							<span :key="item.type + sonIndex" v-html="sonItem"> </span>
						</template>
					</template>

					<template v-else-if="item.type === 'complexList'">
						<ul>
							<li
								v-for="(sonItem, sonIndex) in item.value"
								class="flex-hl-vc flex-hw"
								:key="item.type + sonIndex"
							>
								<span :title="sonItem.tips"
									>{{ sonItem.item
									}}<label class="ml-10">{{ sonItem.params }}</label></span
								>
							</li>
						</ul>
					</template>
					<template v-else-if="item.type === 'list'">
						<ul>
							<li
								v-for="(sonItem, sonIndex) in item.value"
								v-html="sonItem"
								:key="item.type + sonIndex"
							></li>
						</ul>
					</template>
					<template v-else-if="item.type === 'introduction'">
						<p
							v-for="(sonItem, sonIndex) in item.value"
							:key="item.type + sonIndex"
							v-html="sonItem"
						></p>
					</template>
					<template v-else-if="item.type === 'version-timeline'">
						<el-timeline>
							<template v-for="(sonItem, sonIndex) in item.value">
								<el-timeline-item
									:key="sonIndex"
									:timestamp="sonItem.date | formatDateToSecond"
									placement="top"
								>
									<el-card shadow="hover">
										<h4>{{ sonItem.version }}{{ sonItem.versionName }}</h4>
										<p>{{ sonItem.description }}</p>
										<p v-if="sonItem.author && sonItem.date">
											{{ sonItem.author }} 提交于
											{{ sonItem.date | formatDateToSecond }}
										</p>
									</el-card>
								</el-timeline-item>
							</template>
						</el-timeline>
					</template>
					<template v-else-if="item.type === 'project'">
						<div
							class="flex-hl-vc block"
							v-for="sonItem in item.value"
							:key="sonItem.item"
						>
							<div class="flex-hc-vc flex-v">
								<img
									style="width: 100px; height: 100px"
									:src="sonItem.url"
									fit="cover"
								/>
								<span class="mt-5">{{ sonItem.item }}</span>
							</div>
						</div>
					</template>
					<template v-else-if="item.type === 'resume'">
						<template v-if="Array.isArray(item.value) && item.value.length > 0">
							<ul>
								<li
									v-for="sonItem in item.value"
									v-html="sonItem"
									:key="sonItem"
								></li>
							</ul>
						</template>
						<template v-else>
							<p>{{ item.value }}</p>
						</template>
					</template>
				</template>
				<template v-else>
					<view class="gray-text">暂无数据</view>
				</template>
			</view>
		</view>
		<view class="cu-load load-modal col-48e" v-if="!ifShowContent">
			<view class="gray-text">Loading...</view>
		</view>
	</view>
</template>

<script>
import dayjs from 'dayjs'
export default {
	data() {
		return {
			site_url: 'https://klauslaura.cn',
			db_id: 65077635,
			curMovies: 0,
			pageSize: 20,
			type: 'movie',
			recordList: [],
			loadingMoreFlag: false,
			isOver: false,
			ifShowContent: true,
			info: '',
		}
	},
	mounted() {
		this.getRecordList()
	},
	filters: {
		formatDateToSecond: (value) => {
			return dayjs(value).format('YYYY-MM-DD')
		},
	},
	methods: {
		getRecordList() {
			const params = {}
			params.db_id = this.db_id
			params.from = this.curMovies
			params.type = this.type
			wx.request({
				url: `${this.site_url}/wp-content/themes/KlausLab/inc/about/aboutme.json`,
				data: params,
				success: (res) => {
					this.info = res.data ? res.data : ''
					this.ifShowContent = true
				},
			})
		},
		scroll(e) {
			console.log(e)
		},
		loadMore() {
			this.loadingMoreFlag = true
			setTimeout(() => {
				this.getRecordList()
			}, 500)
		},
		getRandomBg() {
			return `bg-${this.ColorList[this.randomNum(0, 11)].name}`
		},
		randomNum(minNum, maxNum) {
			switch (arguments.length) {
				case 1:
					return parseInt(Math.random() * minNum + 1, 10)
					break
				case 2:
					return parseInt(Math.random() * (maxNum - minNum + 1) + minNum, 10)
					break
				default:
					return 0
					break
			}
		},
	},
}
</script>

<style lang="less">
.info-main h3 {
	color: #333333;
	font-weight: 600;
	font-size: 20px;
	padding: 1.5rem 0;
	line-height: 2;
}

.info-main li,
.info-main span,
.info-main p {
	color: #666666;
	line-height: 1.8;
	font-size: 16px;
}

.info-main label {
	color: #999999;
	font-size: 12px;
}

.cu-card {
	margin: 30rpx 0;
	> .cu-item {
		height: auto;
		margin-bottom: 0;
		padding: 30rpx;
	}
}
</style>
