<template>
	<view>
		<cu-custom bgColor="bg-gradual-pink" :isBack="true">
			<block slot="backText">返回</block>
			<block slot="content">{{ posts.title.rendered }}</block>
		</cu-custom>
		<article class="article-container">
			<div class="article-main" v-if="ifShowSingle">
				<div class="entry-header flex-hc-vc">
					<h3 class="entry-title">{{ posts.title.rendered }}</h3>
					<div
						id="banner-bg"
						class="featured-header-image bgc-primary"
						v-html="posts.post_metas.thumbnail"
					></div>
				</div>
				<div class="entry-main">
					<div class="entry-extra p-10">
						<div class="flex-hl-vc">
							<span class="col-bbb flex-hl-vc"> 	<iconfont name="lalaksks-pen-nib mr-5" ></iconfont> {{ posts.post_metas.author }}</span>
							<span class="ml-10 col-bbb flex-hl-vc"> 	<iconfont name="lalaksks-calendar mr-5" ></iconfont> {{ posts.date | formateDate }}</span>
						</div>
						<div class="flex-hr-vc">
							<span class="col-bbb" >字数统计 {{ posts.post_count.text_num }} 字</span >
							<span class="ml-10 col-bbb" >预计阅读时长 {{ posts.post_count.read_time }} 分钟</span >
						</div>
					</div>
					<!-- <div class="entry-content" v-html="posts.content.rendered"></div> -->
					<div class="entry-content">
						<rich-text :nodes="posts.content.rendered"></rich-text>
					</div>
					<!-- <div class="entry-content">
					  <uParse :content="posts.content.rendered" @preview="preview" @navigate="navigate" />
          </div> -->
				</div>
				<div class="entry-footer flex-hc-vc">
					<span class="col-bbb">- end -</span>
					<!-- <template v-if="posts.post_metas.reward === '0' ? false : true">
						<kl-reward></kl-reward>
					</template>
					<template v-if="true">
						<el-button circle @click="updatePost()"
							><i class="el-icon-toilet-paper fs-20"></i
						></el-button>
					</template> -->
				</div>
			</div>
		</article>
	</view>
</template>
<script>
import iconfont from '@/components/iconfont/iconfont'
import dayjs from 'dayjs'

import HTMLParser from '@/common/html-parser.js'
export default {
	data() {
		return {
			site_url: 'https://klauslaura.cn',
			id: '',
			thumbImage: '',
			data: {},
			ifShowSingle: false,
			posts: {
				// post_metas: {
				// 	zan_num: 0,
				// 	comments_num: 0,
				// },
			}
		}
	},
	components: {
		iconfont,
	},
	onLoad(e) {
		this.id = e.id || ''
	},
	mounted() {
		this.getArticleDetail()
		// this.getCommentList()
	},
	methods: {
		getArticleDetail() {
			this.ifShowSingle = false
			uni.showLoading()
			wx.request({
				url: `${this.site_url}/wp-json/wp/v2/posts/${this.id}`,
				success: (res) => {
					this.posts = res.data
					console.log(this.posts)
					this.posts.content.rendered = this.posts.content.rendered
						.replace(/<p([\s\w"=\/\.:;]+)((?:(style="[^"]+")))/gi, '<p')
						.replace(/<p([\s\w"=\/\.:;]+)((?:(class="[^"]+")))/gi, '<p')
						.replace(/<p>/gi, '<p class="p_class fs-15" style="margin-top: 10px">')
						.replace( /<img/gi, '<img style="max-width: 100%; object-fit: cover;"' )
						.replace(/<pre/gi, '<pre style="max-width: 100%; overflow-x: auto"')
						.replace(/复制代码/gi, '')
					this.posts.content.rendered = new HTMLParser(
						this.posts.content.rendered.toString().trim()
					)

					this.ifShowSingle = true
					uni.hideLoading()
				},
			})
		},
	},
	filters: {
		formateDate: (value) => {
			return dayjs(value).format('YYYY-MM-DD')
		},
	}
}
</script>
<style lang="less" scoped>
.article-main {
	ul li {
		list-style-type: disc;

		ul li {
			list-style-type: circle;
		}
	}

	.entry-header {
		position: relative;
		min-height: 240px;
		background-color: #bbbbbb;

		.entry-title {
			a {
				border: none;

				&:visited {
					border: none;
				}
			}

			position: absolute;
			font-size: 1.6rem;
			margin: 0;
			color: #fff;
			word-break: break-word;
			padding: 10px;
			background-color: rgba(0, 0, 0, 0.4);
			border-radius: 4px;
			max-width: 90vw;

			> * {
				position: absolute;
				font-size: 28px;
				margin: 0;
				color: #fff;
				word-break: break-all;
				padding: 10px 1em;
				background-color: rgba(0, 0, 0, 0.4);
				border-radius: 4px;
			}
		}

		.featured-header-image {
			width: 100%;

			img {
				width: 100%;
				max-height: 320px;
				object-fit: cover;
			}
		}
	}

	.entry-content {
		background-color: #fff;
		padding: 5%;
		background-image: linear-gradient(
				90deg,
				var(--linesColor) 3%,
				transparent 0
			),
			linear-gradient(0deg, var(--linesColor) 3%, transparent 0);
		background-position: 50%;
		background-size: 20px 20px;
		word-break: break-word;
		line-height: 1.75;
		font-weight: 400;

		.p_class {
			margin-top: 5px;
		}

		.img_class {
			width: 100%;
			max-width: 50vw;
			height: auto;
			object-fit: cover;
		}

		p {
			font-size: inherit;
			line-height: inherit;
			text-align: justify;

			> * {
				font-size: inherit;
				line-height: inherit;
				text-align: justify;
			}
		}

		ol,
		ul,
		li > * {
			font-size: inherit;
			line-height: inherit;
			text-align: justify;
		}

		p {
			margin: 15px 0;
			font-size: 16px;
		}

		code {
			font-size: 14px;
		}

		.emoji {
			font-size: 18px;
		}

		h1,
		h2,
		h3,
		h4,
		h5 {
			color: var(--primaryText);
			line-height: 1.5;
			margin-top: 35px;
			margin-bottom: 10px;
			padding-bottom: 5px;
		}

		img {
			display: inline-block;
			margin: 15px 0;
			max-width: 100vw;
			height: auto;
			border-radius: 4px;
			cursor: zoom-in;

			&:hover {
				-webkit-box-shadow: 0 5px 10px 0 rgba(160, 160, 160, 0.1);
				-moz-box-shadow: 0 5px 10px 0 rgba(160, 160, 160, 0.1);
				box-shadow: 0 5px 10px 0 rgba(160, 160, 160, 0.1);
			}
		}

		h2 {
			font-size: 22px;

			> * {
				font-size: 22px;
			}
		}

		h3 {
			font-size: 20px;

			> * {
				font-size: 20px;
			}
		}

		h4 {
			font-size: 18px;

			> * {
				font-size: 18px;
			}
		}

		h5 {
			font-size: 16px;
		}

		a {
			text-decoration: none;
			cursor: pointer;
			color: #48e;
			border-bottom: 1px solid #d1e9ff;
		}

		blockquote {
			color: var(--regularText);
			padding: 1px 23px;
			margin: 16px 0;
			border-left: 4px solid #48e;
			background-color: #f8f8f8;

			p {
				text-indent: 0;
			}
		}

		code {
			background-color: #fff5f5;
			color: #ff502c;
			font-size: 0.87em;
			padding: 3px 8px;
		}

		pre {
			padding: 4px 16px;

			> code {
				overflow-x: auto;
				-webkit-overflow-scrolling: touch;
				color: #333;
				background: #f8f8f8;
				padding: 15px;
				margin: 0;
				word-break: normal;
				display: block;
				border-radius: 2px;
			}
		}
	}

	.entry-footer {
		margin: 0;
	}
}
</style>
