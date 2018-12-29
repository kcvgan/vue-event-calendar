<template>
  <div class="events-wrapper" :style="bgColor">
    <v-alert
        :value="isAlert"
        color="error"
        style="background-color: red !important;"
        icon="warning"
        transition="scale-transition"
      >
      {{ i18n[this.locale].noDayAlert }}
    </v-alert>
    <div class="event-panel-header">
      <span class="events-title">{{dayEventsTitle}}</span>
      <v-btn fab dark 
              color="#6AA9C7" 
              depressed 
              class="btn-add-events"
              @click="addEvent"
              :disabled="isDisabled">
        <v-icon>add</v-icon>
      </v-btn>
    </div>
    <div class="cal-events">
      <h1 v-if="noEvents">{{ i18n[this.locale].notHaveEvents }}</h1>
      <slot>
        <div v-for="(event, index) in events" class="event-item" :key="index">
          <cal-event-item :event="event" :index="index" :locale="locale"></cal-event-item>
        </div>
      </slot>
    </div>
  </div>
</template>

<script>
import i18n from '../i18n.js'
import { dateTimeFormatter } from '../tools.js'
import calEventItem from './cal-event-item.vue'
export default {
  name: 'cal-events',
  data () {
    return {
      isAlert: false,
      i18n,
    }
  },
  components: {
    'cal-event-item': calEventItem
  },
  props: {
    title: String,
    dayEvents: {
      type: Object,
      required: true
    },
    locale: {
      type: String,
      required: true
    },
    color: {
      type: String,
      required: true
    }
  },
  computed: {
    dayEventsTitle () {
      if (this.title) return this.title
      if (this.dayEvents.date !== 'all') {
        this.isAlert = false
        let tempDate
        if (this.dayEvents.events.length !== 0) {
          tempDate = Date.parse(new Date(this.dayEvents.events[0].date))
          return dateTimeFormatter(tempDate, i18n[this.locale].fullFormat)
        } else {
          tempDate = dateTimeFormatter(Date.parse(new Date(this.dayEvents.date)), i18n[this.locale].fullFormat)
          console.log(tempDate);
          return `${tempDate}`
        }
      } else {
        return i18n[this.locale].dayEventsTitle
      }
    },
    noEvents() {
        return this.dayEvents.events.length === 0
    },
    addEventTitle() {
      return i18n[this.locale].addEventTitle
    },
    isDisabled () {
      
    },
    events () {
      return this.dayEvents.events
    },
    bgColor () {
      return {backgroundColor: this.color}
    }
  },
  methods: {
    addEvent () {
      if(this.dayEvents.date === 'all') {
        this.isAlert = true
      } else {
        this.$emit('addEvent')
        this.isAlert = false
      }
    }
  }
}
</script>

<style>
.event-panel-header {
  text-align: center;
  overflow: hidden;
}

.btn-add-events {
  float: right;
}

.events-title {
  margin-left: 40px;
  line-height: 68px;
  font-size: 30px;
  font-weight: bold;
  color: white;
}

</style>