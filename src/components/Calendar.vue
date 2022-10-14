<template>
  <h3>カレンダー{{ currentDate }}</h3>
  <div style="max-width: 700px; border-top: 1px solid #eeeeee">
    <div
      v-for="(week, index) in calendars"
      :key="index"
      style="display: flex; border-left: 1px solid #eeeeee"
    >
      <div
        v-for="(day, index) in week"
        :key="index"
        style="
          flex: 1;
          min-height: 125px;
          border-right: 1px solid #eeeeee;
          border-bottom: 1px solid #eeeeee;
        "
      >
        {{ day.date }}
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
  },
  mounted() {
    console.log(this.getCalendar());
  },
  computed: {
    calendars() {
      return this.getCalendar();
    },
  },
};
</script>
