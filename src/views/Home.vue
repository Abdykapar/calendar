<template>
  <div class="home">
    <calendar :events="events"></calendar>
  </div>
</template>

<script>
import moment from "moment";
import Calendar from "../components/Calendar.vue";
export default {
  name: "Home",
  components: { Calendar },
  data() {
    return {
      events: [
        {
          id: 1,
          title: "Presentation",
          date: moment(),
          type: "red",
        },
        {
          id: 2,
          title: "Коммуникационный дизайн среды",
          date: moment(),
          type: "green",
        },
        {
          id: 3,
          title: "Семинары",
          date: moment("08:09:2021 10:23", "DD:MM:YYYY HH:mm"),
          type: "orange",
        },
        {
          id: 4,
          title: "Presentation",
          date: moment("11:09:2021 14:31", "DD:MM:YYYY HH:mm"),
          type: "red",
        },
        {
          id: 5,
          title: "Коммуникационный дизайн среды",
          date: moment("11:09:2021 15:39", "DD:MM:YYYY HH:mm"),
          type: "green",
        },
      ],
    };
  },
};
</script>
<style scoped>
.home {
  display: flex;
  justify-content: center;
  min-height: 100vh;
  align-items: center;
  background: whitesmoke;
}
</style>
