<template>
  <div class="calendar">
    <div class="calendar__head">
      <button class="calendar__button" @click="onPrevMonth">
        <i class="far fa-angle-left"></i>
      </button>
      <span class="calendar__title"
        >{{ currentMonth.format("MMMM") }} {{ getYear() }}</span
      >
      <button class="calendar__button" @click="onNextMonth">
        <i class="far fa-angle-right"></i>
      </button>
    </div>
    <div class="calendar__days">
      <span class="calendar__day">Понедельник</span>
      <span class="calendar__day">Вторник</span>
      <span class="calendar__day">Среда</span>
      <span class="calendar__day">Четверг</span>
      <span class="calendar__day">Пятница</span>
      <span class="calendar__day date--free">Суббота</span>
      <span class="calendar__day date--free">Воскресенье</span>
    </div>
    <div class="calendar__dates">
      <template v-for="(item, i) in weekFirst">
        <div
          :key="i + 'f'"
          class="calendar__date"
          :class="{
            'date--past': isBefore(item),
            'date--weekend': item.day() === 0 || item.day() === 6,
            'date--today': isTodayFromDate(item),
          }"
        >
          <span class="date__title"> {{ item.date() }} </span>
          <template
            v-if="
              events.some(
                (i) => i.date.format('DD:MM:YYYY') === item.format('DD:MM:YYYY')
              )
            "
          >
            <p
              v-for="event in getEvents(item.format('DD:MM:YYYY'))"
              :key="event.id"
              @mouseover="onHover($event, event)"
              @mouseleave="onLeave($event, event)"
              class="date__event"
              :class="'type--' + event.type"
            >
              {{ (event.date.format("HH:mm") + " " + event.title) | cutText }}
            </p>
          </template>
        </div>
      </template>
      <template v-for="item in lastDay.date()">
        <div
          :key="item + '3'"
          class="calendar__date"
          :class="{
            'date--today': isToday(item),
            'date--past': isPastDate(item),
            'date--weekend': isWeekend(item),
          }"
        >
          <span class="date__title"> {{ item }} </span>
          <template
            v-if="
              events.some((i) => i.date.format('DD:MM:YYYY') === getDate(item))
            "
          >
            <p
              v-for="event in getEvents(getDate(item))"
              :key="event.id"
              @mouseover="onHover($event, event)"
              @mouseleave="onLeave($event, event)"
              class="date__event"
              :class="'type--' + event.type"
            >
              {{ (event.date.format("HH:mm") + " " + event.title) | cutText }}
            </p>
          </template>
        </div>
      </template>
      <template v-for="(item, i) in weekLast">
        <div
          :key="i + 'l'"
          class="calendar__date"
          :class="{
            'date--past': isBefore(item),
            'date--weekend': item.day() === 0 || item.day() === 6,
            'date--today': isTodayFromDate(item),
          }"
        >
          <span class="date__title"> {{ item.date() }} </span>
          <template
            v-if="
              events.some(
                (i) => i.date.format('DD:MM:YYYY') === item.format('DD:MM:YYYY')
              )
            "
          >
            <p
              v-for="event in getEvents(item.format('DD:MM:YYYY'))"
              :key="event.id"
              @mouseover="onHover($event, event)"
              @mouseleave="onLeave($event, event)"
              class="date__event"
              :class="'type--' + event.type"
            >
              {{ (event.date.format("HH:mm") + " " + event.title) | cutText }}
            </p>
          </template>
        </div>
      </template>
    </div>
  </div>
</template>

<script>
import moment from "moment";
moment.locale("ru");
export default {
  name: "Calendar",
  props: {
    events: Array,
  },
  data() {
    return {
      currentMonth: moment().startOf("month"),
    };
  },
  computed: {
    lastDay() {
      return this.currentMonth.endOf("month");
    },
    firstDay() {
      return this.currentMonth.startOf("month");
    },
    weekFirst() {
      let dates = [];
      for (
        let i = 1;
        i < (this.firstDay.day() === 0 ? 7 : this.firstDay.day());
        i++
      ) {
        dates.push(moment(this.firstDay).subtract(i, "days"));
      }
      return dates.reverse();
    },
    weekLast() {
      if (this.lastDay.day() === 0) return [];
      let dates = [];
      for (let i = 1; i <= 7 - this.lastDay.day(); i++) {
        dates.push(moment(this.lastDay).add(i, "days"));
      }
      return dates;
    },
  },
  filters: {
    cutText(t) {
      if (t.length > 11) {
        return t.slice(0, 11) + "...";
      }
      return t;
    },
  },
  methods: {
    onHover(e, event) {
      e.target.style.zIndex = "1000";
      e.target.innerText = event.date.format("HH:mm") + " " + event.title;
    },
    onLeave(e, event) {
      e.target.style.zIndex = "unset";
      e.target.innerText = this.cut(
        event.date.format("HH:mm") + " " + event.title
      );
    },
    cut(t) {
      if (t.length > 10) {
        return t.slice(0, 10) + "...";
      }
      return t;
    },
    weekOfMonth(m) {
      return m.week() - moment(m).startOf("month").week() + 1;
    },
    onNextMonth() {
      this.currentMonth = moment(this.currentMonth).add(1, "M");
    },
    onPrevMonth() {
      this.currentMonth = moment(this.currentMonth).subtract(1, "M");
    },
    getEvents(date) {
      return this.events.filter((i) => i.date.format("DD:MM:YYYY") === date);
    },
    getDate(item) {
      return moment(
        `${item}:${this.currentMonth.month() + 1}:${this.currentMonth.year()}`,
        "DD:MM:YYYY"
      ).format("DD:MM:YYYY");
    },
    getYear() {
      return this.currentMonth.year() === moment().year()
        ? ""
        : this.currentMonth.year();
    },
    isWeekend(day) {
      const date = moment(
        `
        ${day}:
        ${this.currentMonth.month() + 1}:
        ${this.currentMonth.year()}`,
        "DD:MM:YYYY"
      );
      return date.day() === 0 || date.day() === 6;
    },
    isToday(item) {
      return (
        item === moment().date() &&
        this.currentMonth.month() === moment().month() &&
        this.currentMonth.year() === moment().year()
      );
    },
    isTodayFromDate(date) {
      const today = moment().format("DD:MM:YYYY");
      return date.isSame(moment(today, "DD:MM:YYYY"));
    },
    isPastDate(item) {
      return (
        (item < moment().date() &&
          this.currentMonth.month() <= moment().month() &&
          this.currentMonth.year() <= moment().year()) ||
        (this.currentMonth.month() < moment().month() &&
          this.currentMonth.year() <= moment().year()) ||
        this.currentMonth.year() < moment().year()
      );
    },
    isBefore(i) {
      const today = moment().format("DD:MM:YYYY");
      return moment(i).isBefore(moment(today, "DD:MM:YYYY"));
    },
  },
};
</script>

<style scoped lang="scss">
.calendar {
  background: white;
  border-radius: 20px;
  padding: 20px;
  &__head {
    display: flex;
    line-height: 120%;
    align-items: center;
  }
  &__button {
    border: none;
    outline: none;
    background: inherit;
    cursor: pointer;
    display: flex;
    align-items: flex-start;
    height: 20px;

    i {
      font-size: 18px;
      color: grey;
    }
  }
  &__title {
    font-weight: bold;
    text-transform: uppercase;
    margin: 0 10px;
  }
  &__head {
    display: flex;
    justify-content: flex-start;
  }
  &__days {
    display: grid;
    grid-template: 1fr / repeat(7, 1fr);
    margin-top: 10px;
    margin-bottom: 5px;
  }
  &__day {
    width: 100px;
    text-align: right;
    font-size: 12px;
    font-weight: bold;
    text-transform: uppercase;
  }
  &__dates {
    display: grid;
    border-radius: 10px;
    grid-template: repeat(5, 1fr) / repeat(7, 1fr);
  }
  &__date {
    border: 2px solid lightgray;
    border-radius: 10px;
    height: 100px;
    width: 100px;
    font-size: 12px;
    font-weight: bold;
    margin: 1px;
    display: flex;
    justify-content: flex-start;
    flex-direction: column;
    align-items: flex-start;
    padding: 5px;
    position: relative;
  }
}
.date {
  &__title {
    display: block;
    width: 100%;
    text-align: right;
  }
  &__event {
    white-space: nowrap;
    border-radius: 5px;
    padding: 0 5px;
    margin-bottom: 4px;
  }
  &--past {
    background: gray;
    color: darkgray;
  }
  &--weekend {
    color: #c8a2c8;
  }
  &--today {
    color: green;
    border-color: #777777;
  }
}
.type {
  &--green {
    background: #72efdd;
    color: green;
  }
  &--red {
    background: #ffccd5;
    color: red;
  }
  &--orange {
    color: orange;
    background: rgba(orange, 0.2);
  }
}
</style>
