<template>
	<view>
		<cu-custom bgColor="bg-gradual-pink" :isBack="true">
			<block slot="backText">返回</block>
			<block slot="content">观看记录</block>
		</cu-custom>
		<scroll-view class="scroll-v list" style="height: 100vh;" @scroll="scroll" enableBackToTop="true" scroll-y @scrolltolower="loadMore()">
			<view class="cu-timeline" v-for="(item,index) in recordList" :key="index">
				<view class="cu-time">{{item.date}}</view>
				<view class="cu-item text-red">
					<view class="content shadow-blur" :class="item.bg">
						<view class="content-title">
							{{item.name}}
						</view>
						<view class="content-main">
							{{item.remark}}
						</view>
					</view>					
				</view>
			</view>
		</scroll-view>
		<view class="cu-load bg-blue" v-if="loadingMoreFlag" :class="!isOver?'loading':'over'"></view>
		<view class="cu-load load-modal col-48e" v-if="!ifShowContent">
			<view class="gray-text">Loading...</view>
		</view>
	</view>
</template>

<script>
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
				ifShowContent: true
			}
		},
		mounted() {
			this.getRecordList()
		},
		computed:{
			
		},
		methods: {
			getRecordList() {
				const self = this
				let params = {};
				params.db_id = this.db_id
				params.from = this.curMovies
				params.type = this.type
				if(this.curMovies === 0) {
					self.ifShowContent = false
				}
				wx.request({
					url: `${this.site_url}/wp-content/themes/KlausLab/inc/douban/douban.php`,
					data: params,
					success: res => {
						const count = res.data.total
						const data = res.data.data.filter(item => {
							return item.name !== ''
						})
						if (data && data.length > 0 ){
							const len = data.length
							for (let i = 0; i < len; i++) {
								data[i].bg = self.getRandomBg()							
							}	
							self.recordList = self.recordList.concat(data)
							if(count - self.curMovies < self.pageSize) {
								this.isOver = true
							}
							self.curMovies += self.pageSize
						}
						self.loadingMoreFlag = false
						self.ifShowContent = true
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
				return `bg-${this.ColorList[this.randomNum(0,11)].name}`
			},
			randomNum(minNum,maxNum) { 
			    switch(arguments.length){ 
			        case 1: 
			            return parseInt(Math.random()*minNum+1,10); 
			        break; 
			        case 2: 
			            return parseInt(Math.random()*(maxNum-minNum+1)+minNum,10); 
			        break; 
			            default: 
			                return 0; 
			            break; 
			    } 
			}
		}
	}
</script>

<style>

</style>
