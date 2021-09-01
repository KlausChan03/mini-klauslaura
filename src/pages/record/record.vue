<template>
	<view>
		<cu-custom bgColor="bg-gradual-pink" :isBack="true">
			<block slot="backText">返回</block>
			<block slot="content">观看记录</block>
		</cu-custom>
		<scroll-view class="scroll-v list" style="height: 100vh;" @scroll="scroll" enableBackToTop="true" scroll-y @scrolltolower="loadMore()">
			<view class="cu-timeline" v-for="(item,index) in recordList">
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
				<!-- <view class="cu-item cur cuIcon-noticefill">
					<view class="content bg-green shadow-blur">
						<text>22:22</text> 【广州市】快件已到达地球
					</view>
				</view>
				<view class="cu-item text-red cuIcon-attentionforbidfill">
					<view class="content bg-red shadow-blur">
						这是第一次，我家的铲屎官走了这么久。久到足足有三天！！
					</view>
				</view>
				<view class="cu-item text-grey cuIcon-evaluate_fill">
					<view class="content bg-grey shadow-blur">
						这是第一次，我家的铲屎官走了这么久。
					</view>
				</view>
				<view class="cu-item text-blue">
					<view class="bg-blue content">
						<text>20:00</text> 【月球】快件已到达月球，准备发往地球
					</view>
					<view class="bg-cyan content">
						<text>10:00</text> 【银河系】快件已到达银河系，准备发往月球
					</view>
				</view> -->
			</view>
		</scroll-view>
		<view class="cu-load bg-blue" v-if="loadingMoreFlag" :class="!isOver?'loading':'over'"></view>
		<view class="cu-load load-modal" v-if="!ifShowContent">
			<view class="cuIcon-emojifill text-orange"></view>
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
