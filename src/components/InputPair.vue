<template>
	<div class="std-col">
		<label :for="identifier">
			<span v-if="labelHasIcon" :class="labelIcon"></span>
			{{ label }}:
		</label>
		<input v-if="inputType !== 'textarea'" :type="inputType"
				:id="identifier" :placeholder="placeholder"
                :autofocus="hasAutofocus" :required="isInputRequired"
				:value="value"
                @input="$emit('input', $event.target.value)">
		<textarea v-else :id="identifier" :placeholder="placeholder"
				:rows="rows" :autofocus="hasAutofocus"
				:required="isInputRequired" :value="value"
				@input="$emit('input', $event.target.value)"></textarea>
	</div>
</template>

<script>
	export default {
		name: "InputPair",
		props: {
			labelHasIcon: {
				type: Boolean,
				default: false
			},
			labelIcon: {
				type: String,
				default: ""
			},
			label: {
				type: String,
				required: true
			},
			placeholder: {
				type: String,
				default: ""
			},
			
			inputType: {
				type: String,
				default: "text"
			},
			identifier: {
				type: String,
				require: true
			},
			isInputRequired: {
				type: Boolean,
				default: true
			},
			hasAutofocus: {
				type: Boolean,
				default: false
			},
			rows: {
				type: Number,
				default: 2
			},
			
			value: null
		},
	}
</script>