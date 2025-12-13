<template>
	<component :is="tag">
		<slot>
			Drop anything here!
		</slot>
	</component>
</template>

<script>
export default {
	wysiwyg: true,
	inject: ['theme', 'breakpoint'],
	props: {
		tag: {
			type: String,
			default: 'div'
		}
	}
};
</script>

<style scoped>
</style>
