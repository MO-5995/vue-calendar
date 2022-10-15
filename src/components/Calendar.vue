<template>
  <h3>Calendar {{ displayDate }}</h3>
  <button @click="prevMonth">Prev Month</button>
  <button @click="nextMonth">Next Month</button>
  <div class="calendar-wrapper">
    <div class="calendar-week">
      <div class="calendar-weekOfDay" v-for="n in 7" :key="n">
        {{ showDayOfWeek(n - 1) }}
      </div>
    </div>
    <div v-for="(week, index) in calendars" :key="index" class="calendar-week">
      <div v-for="(day, index) in week" :key="index" class="calendar-day">
        <p class="calendar-date">{{ day.date }}</p>
      </div>
    </div>
  </div>
</template>

<script>
import moment from "moment";
export default {
  data() {
    return {
      currentDate: moment(),
    };
  },
  methods: {
    getStartDate() {
      let date = moment(this.currentDate);
      date.startOf("month");
      const dayOfWeekNum = date.day();
      return date.subtract(dayOfWeekNum, "days");
    },
    getEndDate() {
      let date = moment(this.currentDate);
      date.endOf("month");
      const dayOfWeekNum = date.day();
      return date.add(6 - dayOfWeekNum, "days");
    },
    getCalendar() {
      let startDate = this.getStartDate();
      const endDate = this.getEndDate();
      const weekNumber = Math.ceil(endDate.diff(startDate, "days") / 7);

      let calendars = [];
      for (let week = 0; week < weekNumber; week++) {
        let weekRow = [];
        for (let day = 0; day < 7; day++) {
          weekRow.push({
            date: startDate.get("date"),
          });
          startDate.add(1, "days");
        }
        calendars.push(weekRow);
      }
      return calendars;
    },
    nextMonth() {
      this.currentDate = moment(this.currentDate).add(1, "month");
    },
    prevMonth() {
      this.currentDate = moment(this.currentDate).subtract(1, "month");
    },
    showDayOfWeek(dayIndex) {
      const week = ["Sun", "Mon", "Tue", "Wed", "Thu", "Fri", "Sat"];
      return week[dayIndex];
    },
  },
  mounted() {
    console.log(this.getCalendar());
  },
  computed: {
    calendars() {
      return this.getCalendar();
    },
    displayDate() {
      return this.currentDate.format("YYYY/M");
    },
  },
};
</script>
<style>
.calendar-wrapper {
  max-width: 700px;
  border-top: 1px solid #eeeeee;
}
.calendar-week {
  display: flex;
  border-left: 1px solid #eeeeee;
}
.calendar-day {
  flex: 1;
  min-height: 125px;
  border-right: 1px solid #eeeeee;
  border-bottom: 1px solid #eeeeee;
}
.calendar-date {
  text-align: center;
}
.calendar-weekOfDay {
  flex: 1;
  border-right: 1px solid #eeeeee;
  text-align: center;
}
</style>
