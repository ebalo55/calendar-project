<template>
	<div class="month-banner">
		<div class="previous" @click="previousMonth"></div>
		<div class="month-selector">
			<div class="changer">
				<span @click="isMonthDropdownVisible = !isMonthDropdownVisible">{{ currentMonthName }} - </span>
				<input type="text" v-model="year" @click="$event.target.select()" @input="handleYearChange">
			</div>
			<div :class="dropdown_class">
				<ul>
					<li v-for="month in filtered_months"
						:key="month"
						@click="changeSelectedMonth(month)">
						{{ month }}
					</li>
				</ul>
			</div>
		</div>
		<div class="next" @click="nextMonth"></div>
	</div>
</template>

<script>
	import moment from "moment";
	
	export default {
		name: "MonthBanner",
		data: function() {
			return {
				months: [],
				currentMonthName: "",
				isMonthDropdownVisible: false,
				year: "",
				timerId: null,
			};
		},
		methods: {
			changeSelectedMonth: function(month) {
				this.currentMonthName = month;
				this.isMonthDropdownVisible = false;
				
				this.$emit("date-change", this.currentMonthName, this.year);
			},
			nextMonth: function() {
				let index_of_current_month = this.months.indexOf(this.currentMonthName);
				if(index_of_current_month +1 >= this.months.length) {
					this.currentMonthName = this.months[0];
					this.year = (+this.year +1).toString();
				}
				else {
					this.currentMonthName = this.months[index_of_current_month +1];
				}
				
				this.$emit("date-change", this.currentMonthName, this.year);
			},
			previousMonth: function() {
				let index_of_current_month = this.months.indexOf(this.currentMonthName);
				if(index_of_current_month -1 < 0) {
					this.currentMonthName = this.months[this.months.length -1];
					this.year = (+this.year -1).toString();
				}
				else {
					this.currentMonthName = this.months[index_of_current_month -1];
				}
				
				this.$emit("date-change", this.currentMonthName, this.year);
			},
			
			handleYearChange: function() {
				if(this.timerId) {
					clearTimeout(this.timerId);
					this.timerId = null;
				}
				
				let _this = this;
				this.timerId = setTimeout(() => {
					_this.$emit("year-change", _this.year);
					_this.timerId = null;
				}, 1e3);
			}
		},
		computed: {
			filtered_months: function() {
				return this.months.filter(value => value !== this.currentMonthName);
			},
			dropdown_class: function() {
				return `dropdown ${this.isMonthDropdownVisible ? "show" : ""}`;
			},
		},
		created() {
			this.currentMonthName = moment().format("MMMM");
			this.year = moment().year();
			this.$emit("date-change", this.currentMonthName, this.year);
			
			// Generate an array containing the name of the months
			for(let i = 0; i < 12; i++) {
				this.months.push(moment().month(i).format("MMMM"));
			}
		}
	}
</script>