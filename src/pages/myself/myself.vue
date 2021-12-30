<template name="user">
	<view class="page myself-page">
		<cu-custom bgColor="#0081FF">
			<block slot="content" style="color:#FFFFFF">个人中心</block>
		</cu-custom>
		<view class="cu-card flex-hc-vc flex-v p-10">
			<view class="cu-item w-1 p-15">
				<view class="flex-hl-vc">
					<image
						:src="userInfo.avatarUrl || defaultAvatar"
						mode=""
						class="user-avatar"
					></image>
					<view class="flex-hc-vt flex-v" style="margin-left: 20px">
						<view class="fs-20">{{ userInfo.nickName }}</view>
						<button
							v-if="!isLogin"
							class="cu-btn bg-blue"
							open-type="getUserInfo"
							bindtap="handleLogin"
							lang="zh_CN"
							@click="handleLogin"
						>
							登录
						</button>
						<button
							v-if="isLogin"
							class="cu-btn bg-blue"
							lang="zh_CN"
							@click="handleLogout"
						>
							登出
						</button>
					</view>
				</view>
			</view>
		</view>
		<view class="cu-bar bg-white solid-bottom">
			<view class="action">
				<text class="cuIcon-title text-orange "></text> 我的记录
			</view>
		</view>
		<view
			class="cu-list grid"
			:class="['col-' + gridCol, gridBorder ? '' : 'no-border']"
		>
			<view
				class="cu-item"
				v-for="(item, index) in cuIconList"
				:key="index"
				@click="goListPage(item.page)"
			>
				<template v-if="index < gridCol * 2 && item.show">
					<view :class="['cuIcon-' + item.cuIcon, 'text-' + item.color]">
						<view class="cu-tag badge" v-if="item.badge != 0">
							<block v-if="item.badge != 1">{{
								item.badge > 99 ? '99+' : item.badge
							}}</block>
						</view>
					</view>
					<text>{{ item.name }}</text>
				</template>
			</view>
		</view>
		<view class="flex-hc-vc p-15">
			<text class="text-Abc">- to be continued -</text>
		</view>
		<view class="cu-load load-modal col-48e" v-if="!ifShowContent">
			<view class="gray-text">Loading...</view>
		</view>
	</view>
</template>

<script>
import defaultAvatar from '../../static/images/default-avatar.png'
export default {
	data() {
		return {
			site_url: 'https://klauslaura.cn',
			defaultAvatar: defaultAvatar,
			userInfo: {},
			isLogin: false,
			ifShowContent: false,
			cuIconList: [
				{
					cuIcon: 'discover',
					color: 'olive',
					badge: 0,
					name: '简介',
					page: 'info',
					show: true,
				},
				{
					cuIcon: 'creativefill',
					color: 'red',
					badge: 120,
					name: '观影',
					page: 'record',
					show: true,
				},
				// {
				// 	cuIcon: 'picfill',
				// 	color: 'yellow',
				// 	badge: 0,
				// 	name: '图像',
				// 	show: false,
				// },
				// {
				// 	cuIcon: 'noticefill',
				// 	color: 'olive',
				// 	badge: 22,
				// 	name: '通知',
				// 	show: false,
				// },
				// {
				// 	cuIcon: 'upstagefill',
				// 	color: 'cyan',
				// 	badge: 0,
				// 	name: '排行榜',
				// 	show: false,
				// },
				// {
				// 	cuIcon: 'discoverfill',
				// 	color: 'purple',
				// 	badge: 0,
				// 	name: '发现',
				// 	show: false,
				// },
				// {
				// 	cuIcon: 'questionfill',
				// 	color: 'mauve',
				// 	badge: 0,
				// 	name: '帮助',
				// 	show: false,
				// },
				// {
				// 	cuIcon: 'commandfill',
				// 	color: 'purple',
				// 	badge: 0,
				// 	name: '问答',
				// 	show: false,
				// },
			],
			modalName: null,
			gridCol: 3,
			gridBorder: true,
			menuBorder: false,
			menuArrow: false,
			menuCard: false,
			skin: false,
			listTouchStart: 0,
			listTouchDirection: null,
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
		setTimeout(() => {
			this.ifShowContent = true
		}, 300)
	},
	methods: {
		goListPage(pageName) {
			uni.navigateTo({
				url: `../${pageName}/${pageName}`,
			})
		},
		handleLogin() {
			const self = this
			wx.getUserProfile({
				desc: '获取用户信息',
				success: (res) => {
					self.userInfo = res.userInfo
					self.isLogin = true
					uni.setStorage({
						key: 'isLogin',
						data: true,
					})
					uni.setStorage({
						key: 'userInfo',
						data: res.userInfo,
					})
				},
			})
		},
		handleLogout() {
			this.userInfo = {}
			this.isLogin = false
			uni.removeStorage({
				key: 'isLogin',
			})
			uni.removeStorage({
				key: 'userInfo',
			})
			wx.clearStorageSync()
			wx.showToast({
				title: '清除完毕',
			})
		},
		// doWeixinGetUserInfo() {
		// 	let code = ''
		// 	const _getLoginCode = new Promise((resolve) => {
		// 		wx.login({
		// 			success: (res) => {
		// 				code = res.code
		// 			},
		// 		})
		// 	})

		// 	const _getUserProfile = new Promise((resolve) => {
		// 		wx.getUserProfile({
		// 			desc: '获取用户信息',
		// 			success: (res) => {
		// 				_login(res)
		// 			},
		// 		})
		// 	})

		// 	const _login = (data) => {
		// 		let userInfoFromWx = data.userInfo
		// 		wx.request({
		// 			url: `${this.site_url}/wp-json/wp/v2/openid`,
		// 			method: 'POST',
		// 			data: {
		// 				username: 'Klaus',
		// 				password: 'roger0629',
		// 			},
		// 			success: (res) => {
		// 				// debugger
		// 				// if (res.data && res.data.data) {
		// 				// 	let data = res.data.data
		// 				// 	let token = data.token;
		// 				// 	let userInfoFromSelf = data.user;
		// 				// 	this.$store.commit('token', token)
		// 				// 	// debugger
		// 				// 	if (userInfoFromSelf) {
		// 				// 		this.$store.commit('login', true)
		// 				// 		this.$store.commit('register', true)
		// 				// 		this.$store.commit('getUserInfo', data.user)
		// 				// 	} else {
		// 				// 		_register(userInfoFromWx, token)
		// 				// 	}
		// 				// }
		// 			},
		// 		})
		// 	}

		// 	const _register = (params1, params2) => {
		// 		let params = { ...params1, token: params2 }
		// 		wx.request({
		// 			url: `${this.site_url}/wp-json/wp/v2/posts`,
		// 			data: params,
		// 			success: (res) => {
		// 				// _login()
		// 				let data = res.data.data
		// 				this.$store.commit('login', true)
		// 				this.$store.commit('register', true)
		// 				this.$store.commit('getUserInfo', data.user)
		// 			},
		// 		})
		// 	}

		// 	Promise.all([_getLoginCode, _getUserProfile]).then((result) => {
		// 		const [code, userProfile] = result
		// 		// wx.BaaS.auth.updateUserInfo(userProfile, {
		// 		// 	code
		// 		// }).then(res => {
		// 		// 	// user 包含用户完整信息，详见下方描述
		// 		// }, err => {
		// 		// 	// **err 有两种情况**：用户拒绝授权，HError 对象上会包含基本用户信息：id、openid、unionid；其他类型的错误，如网络断开、请求超时等，将返回 HError 对象（详情见下方注解）
		// 		// })
		// 	})
		// },
	},
}
</script>

<style lang="less">
.myself-page {
	flex: 1;
	flex-direction: column;
	overflow: hidden;
	background-color: #ecebeb;
	/* #ifdef MP-ALIPAY || MP-BAIDU */
	height: 100vh;
	/* #endif */
	height: 100%;

	.user-avatar {
		width: 140rpx;
		height: 140rpx;
		border-radius: 50%;
	}
}
</style>
