<template>
	<transition name="fade">
		<div class="modal-wrapper" v-show="isVisible">
			<div class="modal">
				<header>
					<span>{{ title }}</span>
					<div class="close" @click="$emit('close')"></div>
				</header>
				<section>
					<slot></slot>
				</section>
			</div>
		</div>
	</transition>
</template>

<script>
	export default {
		name: "Modal",
		props: {
			title: {
				type: String,
				required: true
			},
			isVisible: {
				type: Boolean,
				required: true
			}
		}
	}
</script>