<template>
  <div class="events-wrapper" :style="bgColor">
    <v-alert
      :value="isAlert"
      color="error"
      style="background-color: #F34236 !important; border-radius: 10px;"
      icon="warning"
      transition="scale-transition"
    >{{ i18n[this.locale].noDayAlert }}</v-alert>
    <div class="event-panel-header">
      <span class="events-title">{{dayEventsTitle}}</span>
      <v-btn
        fab
        dark
        color="#6AA9C7"
        depressed
        class="btn-add-events"
        @click="addEvent"
        :disabled="isDisabled"
      >
        <v-icon>add</v-icon>
      </v-btn>
    </div>
    <!-- <div class="cal-events">
      <h1 v-if="noEvents">{{ i18n[this.locale].notHaveEvents }}</h1>
      <slot>
        <div v-for="(event, index) in events" class="event-item" :key="index">
          <cal-event-item :event="event" :index="index" :locale="locale"></cal-event-item>
        </div>
      </slot>
    </div>-->
    <swipe-list
      class="card"
      :disabled="!enabled"
      :items="events"
      transition-key="id"
      @swipeout:contentclick="contentClick"
      @swipeout:click="itemClick"
      @swipeout:doubleclick="itemDblClick"
    >
      <template slot-scope="{ item, index, revealLeft, revealRight, close }">
        <div class="card-content">
          <h2 style="text-align: left;">{{ `${item.title}`}}</h2>
          <h4 style="text-align: left;">{{ `${formatTime(item.time)} ${item.date}` }}</h4>
          <p>{{ item.desc }}</p>
        </div>
      </template>
      <template slot="right" slot-scope="{ item }">
        <div class="swipeout-action" style="background-color: #6AA9C7; color: white;" @click="edit(item)">
          <i class="fa fa-edit fa-2x"></i>
        </div>
        <div class="swipeout-action red" @click="remove(item)">
          <i class="fa fa-trash fa-2x"></i>
        </div>
      </template>
      <div slot="empty" style="text-align: center; font-size: 18px; line-height: 40px;">{{ i18n[this.locale].notHaveEvents }}</div>
    </swipe-list>
  </div>
</template>

<script>
import i18n from "../i18n.js";
import { dateTimeFormatter, formatTime } from "../tools.js";
import calEventItem from "./cal-event-item.vue";

import SwipeList from "./SwipeList.vue";
import SwipeOut from "./SwipeOut.vue";

export default {
  name: "cal-events",
  data() {
    return {
      isAlert: false,
      i18n,
      enabled: true,
    };
  },
  components: {
    "cal-event-item": calEventItem,
    SwipeOut,
    SwipeList
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
    dayEventsTitle() {
      if (this.title) return this.title;
      if (this.dayEvents.date !== "all") {
        this.isAlert = false;
        let tempDate;
        if (this.dayEvents.events.length !== 0) {
          tempDate = Date.parse(new Date(this.dayEvents.events[0].date));
          return dateTimeFormatter(tempDate, i18n[this.locale].fullFormat);
        } else {
          tempDate = dateTimeFormatter(
            Date.parse(new Date(this.dayEvents.date)),
            i18n[this.locale].fullFormat
          );
          console.log(tempDate);
          return `${tempDate}`;
        }
      } else {
        return i18n[this.locale].dayEventsTitle;
      }
    },
    noEvents() {
      return this.dayEvents.events.length === 0;
    },
    addEventTitle() {
      return i18n[this.locale].addEventTitle;
    },
    isDisabled() {},
    events() {
      return this.dayEvents.events;
    },
    bgColor() {
      return { backgroundColor: this.color };
    }
  },
  mounted() {
    // ideally should be in some global handler/store
    window.addEventListener("keydown", this.onKeyDown);
    window.addEventListener("keyup", this.onKeyUp);
  },
  beforeDestroy() {
    window.removeEventListener("keydown", this.onKeyDown);
    window.removeEventListener("keyup", this.onKeyUp);
  },
  methods: {
    addEvent() {
      if (this.dayEvents.date === "all") {
        this.isAlert = true;
      } else {
        this.$emit("addEvent");
        this.isAlert = false;
      }
    },
    formatTime,
    remove(item) {
      this.$emit('removeEvent', item)
    },
    edit(item) {
      this.$emit('editEvent', item)
    },
    contentClick(e) {
      this.$emit('eventClick', event.item)
    },
    itemClick(e) {
      console.log(e, "item click");
    },
    itemDblClick(e) {
      console.log(e, "item double click");
    },
    fbClick(e) {
      console.log(e, "First Button Click");
    },
    sbClick(e) {
      console.log(e, "Second Button Click");
    },
    // keyboard
    onKeyDown(e) {
      if (e.keyCode !== 16) return;
      this.enabled = false;
    },
    onKeyUp(e) {
      if (e.keyCode !== 16) return;
      this.enabled = true;
    }
  }
};
</script>

<style lang="scss">
.event-panel-header {
  text-align: center;
  overflow: hidden;
}

.btn-add-events {
  float: right;
}

.btn-all-events {
  float: left;
}

.events-title {
  margin-left: 40px;
  line-height: 68px;
  font-size: 30px;
  font-weight: bold;
  color: white;
}

@import url("https://cdnjs.cloudflare.com/ajax/libs/font-awesome/4.7.0/css/font-awesome.min.css");

.swipeout-list {
  display: flex;
  flex-direction: column;
}
.swipeout-action {
  display: flex;
  align-items: center;
  padding: 0 3rem;
  cursor: pointer;
  left: 0;
  // https://developer.apple.com/design/human-interface-guidelines/ios/visual-design/color/
  &.blue {
    color: white;
    background-color: rgb(0, 122, 255);
    &:hover {
      background-color: darken(rgb(0, 122, 255), 5%);
    }
  }
  &.purple {
    color: white;
    background-color: rgb(88, 86, 214);
    &:hover {
      background-color: darken(rgb(88, 86, 214), 5%);
    }
  }
  &.red {
    color: white;
    background-color: rgb(255, 59, 48);
    &:hover {
      background-color: darken(rgb(255, 59, 48), 5%);
    }
  }
  &.green {
    color: white;
    background-color: rgb(76, 217, 100);
    &:hover {
      background-color: darken(rgb(76, 217, 100), 5%);
    }
  }
}
.swipeout-list-item {
  flex: 1;
  border-bottom: 1px solid lightgray;
  border-radius: 10px;
  &:last-of-type {
    border-bottom: none;
  }
}
.card {
  width: 100%;
  background-color: white;
  border-radius: 10px;
  box-shadow: none;
}
.card-content {
  padding: 1rem;
  color: black !important;
}
</style>