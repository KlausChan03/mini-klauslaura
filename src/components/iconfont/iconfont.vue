<template>
	<text
		class="iconfont lalaksks"
		:class="name"
		:style="[{ color: color, fontSize: size + 'px' }]"
		@click="$emit('click', $event)"
	></text>
</template>

<script>
export default {
	props: {
		name: String,
		color: {
			type: String,
			default: '#909399',
		},
        size: {
			type: String,
			default: '14px',
		},
	},
}
</script>

<style scoped>
@import url('./iconfont.css');

.iconfont {
	display: flex;
	align-items: center;
	justify-content: center;
	font-size: inherit;
}
</style>
