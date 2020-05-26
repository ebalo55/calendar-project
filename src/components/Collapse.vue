<template>
	<ul class="events-list">
		<li :class="opened" @click="isOpen = !isOpen">
			{{ eventTitle }}
			<span class="arrow"></span>
		</li>
		<li :class="extra_information_class">
			<div class="left-col">
				<div class="data-display">
					<label>Starts at:</label>
					{{ startingTime }}
				</div>
				<div class="data-display">
					<label>Location:</label>
					{{ location }}
				</div>
			</div>
			<div class="right-col">
				<label>Description:</label>
				<pre>{{ description }}</pre>
			</div>
		</li>
	</ul>
</template>

<script>
	export default {
		name: "Collapse",
		props: {
			isOpen: {
				type: Boolean,
				required: true
			},
			
			eventTitle: {
				type: String,
				required: true
			},
			startingTime: {
				type: String,
				required: true
			},
			location: {
				type: String,
				required: true
			},
			description: {
				type: String,
				required: true
			}
		},
		computed: {
			opened: function() {
				return this.isOpen ? "open" : "";
			},
			extra_information_class: function() {
				return `cols unequal-division ${this.isOpen ? "open" : ""}`;
			}
		}
	}
</script>