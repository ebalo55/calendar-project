<template>
    <div id="app" class="view">
        <month-banner @date-change="handleDateChange"></month-banner>
        <div class="month-card-wrapper">
            <month-day v-for="index in days_number"
                   :key="index"
                   :day-index="index"
                   :saved-events="getSavedEvents(index)"
                   @add-day-event="openModal(addEventModal, index)"
                   @complete-event-list="(day, title) => openModal(expandEventModal, day, title)"></month-day>
        </div>
        <modal :title="addEventModal.title" :is-visible="addEventModal.isOpen"
            @close="closeModal(addEventModal)">
            <div class="cols">
                <div class="left-col">
                    <input-pair identifier="event-title" placeholder="Event title"
                                :has-autofocus="true" label="Event title"
                                v-model="addEventModal.values.title"></input-pair>
                    <input-pair identifier="event-position" placeholder="Event position"
                                label="Event position" :label-has-icon="true"
                                label-icon="location" v-model="addEventModal.values.position"></input-pair>
                    <input-pair identifier="event-time" placeholder="Event starting time"
                                label="Event starting time" :label-has-icon="true"
                                label-icon="clock" input-type="time"
                                v-model="addEventModal.values.time"></input-pair>
                </div>
                <div class="right-col">
                    <input-pair identifier="event-description" placeholder="Event description"
                                label="Event description" :label-has-icon="true"
                                label-icon="script" :rows="9"
                                input-type="textarea" v-model="addEventModal.values.description"></input-pair>
                </div>
            </div>
            <div class="buttons-container">
                <button class="cancel" @click="closeModal(addEventModal)">Cancel</button>
                <button class="confirm" @click="handleAddEvent">Add event</button>
            </div>
        </modal>
        <modal :title="expandEventModal.title" :is-visible="expandEventModal.isOpen"
               @close="closeModal(expandEventModal)">
            <div class="single-col">
                <collapse v-for="(item, index) of complete_events_list"
                    :event-title="item.title" :is-open="item.open"
                    :starting-time="item.time" :location="item.location"
                    :description="item.description" :key="index"></collapse>
            </div>
        </modal>
    </div>
</template>

<script>
import moment from "moment";
import MonthBanner from "@/components/MonthBanner";
import MonthDay from "@/components/MonthDay";
import Modal from "@/components/Modal";
import InputPair from "@/components/InputPair";
import Collapse from "@/components/Collapse";

export default {
    name: 'App',
    components: {Collapse, InputPair, Modal, MonthDay, MonthBanner},
    data: function() {
        return {
            days: null,
            currentWorkingDay: null,
            monthName: "",
            year: "",
            allTimeEvents: {
                "5-May-2020": {
                    events: [
                        { time: "14:48", title: "Small event", description: "This is a really small event, nothing exciting. Few people will come.", location: "My home", open: false },
                        { time: "14:50", title: "Medium event", description: `This event is quite important but not the best one.`, location: "Your home", open: false },
                        { time: "14:52", title: "Large event ", description: "Oh this starts sound pretty!", location: "The college", open: false },
                        { time: "14:54", title: "X-Large event", description: "This is pretty large isn't it?", location: "The mall", open: false },
                        { time: "14:56", title: "XX-Large event", description: "Oh, damn i can't even see my hands here!", location: "The disco", open: false },
                        { time: "14:58", title: "XXX-Large event", description: "This is really awesome and... HUGE", location: "The stadium", open: false },
                    ]
                }
            },
            addEventModal: {
                title: "Add event",
                isOpen: false,
                values: {
                    title: "",
                    position: "",
                    time: "",
                    description: ""
                }
            },
            expandEventModal: {
                title: "Your event for ...",
                isOpen: false,
            },
        };
    },
    methods: {
        computeEventIndexFromTitle: function(title) {
            return this.allTimeEvents[this.complete_date].events.findIndex(value => value.title === title);
        },
        resetCollapsableState: function() {
            this.allTimeEvents[this.complete_date].events.forEach(value => value.open = false);
        },
        
        openModal: function(target, dayIndex, title) {
            target.isOpen = true;
            if(dayIndex) {
                this.currentWorkingDay = dayIndex;
            }
            
            if(target === this.expandEventModal) {
                target.title = `Your event for ${this.currentWorkingDay} ${this.monthName} ${this.year}`;
            }
            
            if(title) {
                this.allTimeEvents[this.complete_date].events[this.computeEventIndexFromTitle(title)].open = true;
            }
        },
        closeModal: function(target) {
            target.isOpen = false;
            if(this.allTimeEvents) {
                this.resetCollapsableState();
            }
        },
        
        handleDateChange: function(month, year) {
            this.monthName = month;
            this.year = year;
            this.days = moment().month(this.monthName).year(this.year).daysInMonth();
        },
        handleAddEvent: function() {
            if(Object.prototype.hasOwnProperty.call(this.allTimeEvents, this.complete_date)) {
                this.allTimeEvents[this.complete_date].events.push({
                    time: this.addEventModal.values.time,
                    title: this.addEventModal.values.title,
                    description: this.addEventModal.values.description,
                    location: this.addEventModal.values.position,
                    open: false
                });
            }
            else {
                this.allTimeEvents[this.complete_date] = {
                    events: [
                        {
                            time: this.addEventModal.values.time,
                            title: this.addEventModal.values.title,
                            description: this.addEventModal.values.description,
                            location: this.addEventModal.values.position,
                            open: false
                        }
                    ]
                };
            }
    
            /* Tweak to force the refresh of the displayed month using a function.
             * In a smarter implementation an array should be used but for lots of information and events this
             * can became heavy
             */
            let _this = this,
                    tmp = this.allTimeEvents;
            this.allTimeEvents = false;
            /* This small timer let Vue refresh all the states linked to "allTimeEvents",
			 * once it is fired the original object is restored and the states are
			 * refreshed again.
			 *
			 * ****************************************************
			 * *    THE USAGE OF THIS METHOD SHOULD BE AVOIDED    *
			 * ****************************************************
			 */
            setTimeout(() => {
                _this.allTimeEvents = tmp;
                _this.resetCollapsableState();
            }, 100);
            
            
            for(let prop in this.addEventModal.values) {
                this.addEventModal.values[prop] = "";
            }
            this.closeModal(this.addEventModal);
        },
    
        getSavedEvents: function(index) {
            if(this.allTimeEvents) {
                let date = `${index}-${this.monthName}-${this.year}`;
                return Object.prototype.hasOwnProperty.call(this.allTimeEvents, date) ? this.allTimeEvents[date].events : []
            }
            return [];
        },
    },
    computed: {
        days_number: function() {
            return Array.from({ length: this.days },(v, k) => k + 1);
        },
        complete_date: function() {
            return `${this.currentWorkingDay}-${this.monthName}-${this.year}`;
        },
        complete_events_list: function() {
            return Object.prototype.hasOwnProperty.call(this.allTimeEvents, this.complete_date) ?
                    this.allTimeEvents[this.complete_date].events : [];
        }
    },
    created() {
        this.days = moment().daysInMonth();
    }
}
</script>
