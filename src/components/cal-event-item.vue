<template>
  <div class="wrapper list-item">
    <div class="item">
      <h3 class="title">{{event.title}}</h3>
      <p class="time">{{formatTime(event.time) + dateTimeFormatter(Date.parse(new Date(event.date)),i18n[locale].fullFormat)}}</p>
      <p class="desc" v-html="event.desc" />
    </div>
  </div>
</template>
<script>
import i18n from '../i18n.js'
import { dateTimeFormatter, formatTime } from '../tools.js'
export default {
  data () {
    return {
      i18n
    }
  },
  props: {
    event: {
      type: Object,
      required: true
    },
    index: {
      type: Number,
      required: true
    },
    locale: {
      type: String,
      required: true
    }
  },
  methods: {
    dateTimeFormatter,
    formatTime
  }
}
</script>

<style>
</style>