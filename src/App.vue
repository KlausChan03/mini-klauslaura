<script>
import { mapMutations } from 'vuex'
import Vue from 'vue'

export default {
	onLaunch: function() {
		console.log('App Launch')

		uni.getSystemInfo({
			success: function(e) {
				// #ifndef MP
				Vue.prototype.StatusBar = e.statusBarHeight
				if (e.platform == 'android') {
					Vue.prototype.CustomBar = e.statusBarHeight + 50
				} else {
					Vue.prototype.CustomBar = e.statusBarHeight + 45
				}
				// #endif

				// #ifdef MP-WEIXIN
				Vue.prototype.StatusBar = e.statusBarHeight
				let custom = wx.getMenuButtonBoundingClientRect()
				Vue.prototype.Custom = custom
				Vue.prototype.CustomBar = custom.bottom + custom.top - e.statusBarHeight
				// #endif

				// #ifdef MP-ALIPAY
				Vue.prototype.StatusBar = e.statusBarHeight
				Vue.prototype.CustomBar = e.statusBarHeight + e.titleBarHeight
				// #endif
			},
		})
		// #ifdef APP-PLUS
		// App平台检测升级，服务端代码是通过uniCloud的云函数实现的，详情可参考：https://ext.dcloud.net.cn/plugin?id=2226
		if (plus.runtime.appid !== 'HBuilder') {
			// 真机运行不需要检查更新，真机运行时appid固定为'HBuilder'，这是调试基座的appid
			uni.request({
				url:
					'https://7a3e3fa9-7820-41d0-be80-11927ac2026c.bspapp.com/http/update', //检查更新的服务器地址
				data: {
					appid: plus.runtime.appid,
					version: plus.runtime.version,
					imei: plus.device.imei,
				},
				success: (res) => {
					if (res.statusCode == 200 && res.data.isUpdate) {
						// 提醒用户更新
						uni.showModal({
							title: '更新提示',
							content: res.data.note ? res.data.note : '是否选择更新',
							success: (ee) => {
								if (ee.confirm) {
									plus.runtime.openURL(res.data.url)
								}
							},
						})
					}
				},
			})
		}

		// 一键登录预登陆，可以显著提高登录速度
		uni.preLogin({
			provider: 'univerify',
			success: (res) => {
				// 成功
				this.setUniverifyErrorMsg()
				console.log('preLogin success: ', res)
			},
			fail: (res) => {
				this.setUniverifyLogin(false)
				this.setUniverifyErrorMsg(res.errMsg)
				// 失败
				console.log('preLogin fail res: ', res)
			},
		})
		// #endif

		Vue.prototype.ColorList = [
			{
				title: '嫣红',
				name: 'red',
				color: '#e54d42',
			},
			{
				title: '桔橙',
				name: 'orange',
				color: '#f37b1d',
			},
			{
				title: '明黄',
				name: 'yellow',
				color: '#fbbd08',
			},
			{
				title: '橄榄',
				name: 'olive',
				color: '#8dc63f',
			},
			{
				title: '森绿',
				name: 'green',
				color: '#39b54a',
			},
			{
				title: '天青',
				name: 'cyan',
				color: '#1cbbb4',
			},
			{
				title: '海蓝',
				name: 'blue',
				color: '#0081ff',
			},
			{
				title: '姹紫',
				name: 'purple',
				color: '#6739b6',
			},
			{
				title: '木槿',
				name: 'mauve',
				color: '#9c26b0',
			},
			{
				title: '桃粉',
				name: 'pink',
				color: '#e03997',
			},
			{
				title: '棕褐',
				name: 'brown',
				color: '#a5673f',
			},
			{
				title: '浅灰',
				name: 'mini',
				color: '#f0f0f0',
			},
			{
				title: '玄灰',
				name: 'grey',
				color: '#8799a3',
			},
			{
				title: '草灰',
				name: 'gray',
				color: '#aaaaaa',
			},
			{
				title: '墨黑',
				name: 'black',
				color: '#333333',
			},
			{
				title: '雅白',
				name: 'white',
				color: '#ffffff',
			},
		]
	},
	onShow: function() {
		console.log('App Show')
	},
	onHide: function() {
		console.log('App Hide')
	},
	globalData: {
		test: '',
	},
	methods: {
		...mapMutations(['setUniverifyErrorMsg', 'setUniverifyLogin']),
	},
}
</script>

<style>
@import 'colorui/main.css';
@import 'colorui/icon.css';
@import 'static/style.css';
/* #ifndef APP-PLUS-NVUE */
/* uni.css - 通用组件、模板样式库，可以当作一套ui库应用 */
@import './common/uni.css';

/* H5 兼容 pc 所需 */
/* #ifdef H5 */
@media screen and (min-width: 768px) {
	body {
		overflow-y: scroll;
	}
}

/* 顶栏通栏样式 */
/* .uni-top-window {
	    left: 0;
	    right: 0;
	} */

uni-page-body {
	background-color: #f5f5f5 !important;
	min-height: 100% !important;
	height: auto !important;
}

.uni-top-window uni-tabbar .uni-tabbar {
	background-color: #fff !important;
}

.uni-app--showleftwindow .hideOnPc {
	display: none !important;
}
/* #endif */

/* 以下样式用于 hello uni-app 演示所需 */
page {
	background-color: #efeff4;
	height: 100%;
	font-size: 28rpx;
	line-height: 1.8;
}
.fix-pc-padding {
	padding: 0 50px;
}
.uni-header-logo {
	padding: 30rpx;
	flex-direction: column;
	justify-content: center;
	align-items: center;
	margin-top: 10rpx;
}

.uni-header-image {
	width: 100px;
	height: 100px;
}

.uni-hello-text {
	color: #7a7e83;
}

.uni-hello-addfile {
	text-align: center;
	line-height: 300rpx;
	background: #fff;
	padding: 50rpx;
	margin-top: 10px;
	font-size: 38rpx;
	color: #808080;
}
/* #endif*/
</style>
