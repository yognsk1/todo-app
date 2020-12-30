<template>
  <div class="main-panel">
    <div class="panels">
      <TodoList />
    </div>
    <div class="panels">
      <FullCalendar v-bind:options="calendarOptions" />
    </div>
  </div>
</template>

<script>
import Vue from "vue";
import Component from "vue-class-component";
import FullCalendar from "@fullcalendar/vue";
import dayGridPlugin from "@fullcalendar/daygrid";
import interactionPlugin from "@fullcalendar/interaction";

@Component({
  components: {
    TodoList: () => import("./components/TodoList.vue"),
    FullCalendar,
  },
})
class App extends Vue {
  calendarOptions = {
    plugins: [dayGridPlugin, interactionPlugin],
    initialView: "dayGridMonth",
    events: [{ title: "event 2", date: "2020-12-10" }],
    dateClick: this.handleDateClick,
  };

  handleDateClick(arg) {
    console.log(arg);
  }
  addEvent({ title, date }) {
    console.log("parent method call", event);
    this.calendarOptions.events.push({
      title,
      date,
    });
  }
}
export default App;
</script>

<style scoped>
.main-panel {
  display: flex;
}
.panels {
  width: 50%;
  padding: 5px;
}
</style>
