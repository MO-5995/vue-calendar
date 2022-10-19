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
      <div
        :class="{ outside: currentMonth !== day.month }"
        v-for="(day, index) in week"
        :key="index"
        class="calendar-day"
      >
        <p class="calendar-date">{{ day.day }}</p>
        <div v-for="dayEvent in day.dayEvents" :key="dayEvent.id">
          <div
            class="calendar-event"
            :style="`width:${dayEvent.width}%;background-color:${dayEvent.color}`"
            draggable="true"
          >
            {{ dayEvent.name }}
          </div>
        </div>
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
      events: [
        {
          id: 1,
          name: "ミーティング",
          start: "2022/10/01",
          end: "2022/10/01",
          color: "blue",
        },
        {
          id: 2,
          name: "イベント",
          start: "2022/10/05",
          end: "2022/10/05",
          color: "limegreen",
        },
        {
          id: 3,
          name: "会議",
          start: "2022/10/10",
          end: "2022/10/10",
          color: "deepskyblue",
        },
        {
          id: 4,
          name: "有給",
          start: "2022/10/19",
          end: "2022/10/19",
          color: "dimgray",
        },
        {
          id: 5,
          name: "海外旅行",
          start: "2022/10/26",
          end: "2022/11/05",
          color: "navy",
        },
        {
          id: 6,
          name: "誕生日",
          start: "2022/11/08",
          end: "2022/11/08",
          color: "orange",
        },
        {
          id: 7,
          name: "イベント",
          start: "2022/10/22",
          end: "2022/10/23",
          color: "limegreen",
        },
        {
          id: 8,
          name: "出張",
          start: "2022/11/10",
          end: "2022/11/13",
          color: "teal",
        },
        {
          id: 9,
          name: "客先訪問",
          start: "2022/11/20",
          end: "2022/11/20",
          color: "red",
        },
        {
          id: 10,
          name: "パーティ",
          start: "2022/12/01",
          end: "2022/12/01",
          color: "royalblue",
        },
        {
          id: 10,
          name: "誕生日",
          start: "2022/12/01",
          end: "2022/12/01",
          color: "orange",
        },
      ],
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
      let calendarDate = this.getStartDate();
      for (let week = 0; week < weekNumber; week++) {
        let weekRow = [];
        for (let day = 0; day < 7; day++) {
          let dayEvents = this.getDayEvents(calendarDate, day);
          weekRow.push({
            day: calendarDate.get("date"),
            month: calendarDate.format("YYYY/MM"),
            dayEvents,
          });
          calendarDate.add(1, "days");
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
    getDayEvents(date, day) {
      let dayEvents = [];
      this.sortedEvents.filter((event) => {
        let startDate = moment(event.start).format("YYYY/MM/DD");
        let endDate = moment(event.end).format("YYYY/MM/DD");
        let Date = date.format("YYYY/MM/DD");
        if (startDate <= Date && endDate >= Date) {
          if (startDate === Date) {
            let width = this.getEventWidth(startDate, endDate, day);
            dayEvents.push({ ...event, width });
          } else if (day === 0) {
            let width = this.getEventWidth(startDate, endDate, day);
            dayEvents.push({ ...event, width });
          }
        }
      });
      return dayEvents;
    },
    getEventWidth(end, start, day) {
      let betweenDays = moment(end).diff(moment(start), "days");
      if (betweenDays > 6 - day) {
        return (6 - day) * 100 + 95;
      } else {
        return betweenDays * 100 + 95;
      }
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
    currentMonth() {
      return this.currentDate.format("YYYY/MM");
    },
    sortedEvents() {
      return this.events.slice().sort(function (a, b) {
        let startDate = moment(a.start).format("YYYY/MM/DD");
        let startDate_2 = moment(b.start).format("YYYY/MM/DD");
        if (startDate < startDate_2) return -1;
        if (startDate > startDate_2) return 1;
        return 0;
      });
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
.outside {
  background-color: #f7f7f7;
}
.calendar-event {
  color: white;
  margin-bottom: 1px;
  height: 25px;
  line-height: 25px;
}
</style>
