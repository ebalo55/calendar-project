<template>
	<div class="day" :data-day-index="dayIndex">
		<h3>
			{{ day_name }}
			<span class="add-event" @click="$emit('add-day-event', dayIndex)"></span>
		</h3>
		<section>
			<ul>
				<li v-for="event of preview_events"
					:key="event.title"
					@click="$emit('complete-event-list', dayIndex, event.title)">
					{{ event.time }} - {{ event.title }}
				</li>
				<li v-if="preview_events.length < savedEvents.length" class="more"
					@click="$emit('complete-event-list', dayIndex)">
					More ...
				</li>
			</ul>
		</section>
	</div>
</template>

<script>
	import moment from "moment";
	
	export default {
		name: "MonthDay",
		props: {
			dayIndex: {
				type: Number,
				required: true
			},
			savedEvents: {
				type: Array,
				required: true
			}
		},
		computed: {
			day_name: function() {
				return moment().date(this.dayIndex).format("dddd");
			},
			preview_events: function() {
				let result = [];
				for(let index = 0; this.savedEvents.length > index && index < 5; index++) {
					result.push(this.savedEvents[index]);
				}
				return result;
			}
		}
	}
</script>