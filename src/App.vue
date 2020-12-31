<template>
  <div class="main-panel">
    <div class="panels">
      <TodoList />
    </div>
    <div class="panels">
      <FullCalendarComponent
        v-on:calanderEventItemClicked="calanderEventItemClickedListener"
        :scheduleEventList="scheduleEventList"
        @tooltipAnchorClicked="onTooltipAnchorClicked"
      />
    </div>
  </div>
</template>

<script>
import Vue from "vue";
import Component from "vue-class-component";

@Component({
  components: {
    TodoList: () => import("./components/TodoList.vue"),
    FullCalendarComponent: () =>
      import("./components/FullCalendarComponent.vue"),
  },
})
class App extends Vue {
  scheduleEventList = [];

  calanderEventItemClickedListener(eventObject) {
    console.log("calanderEventItemClickedListener", eventObject);
  }
  onTooltipAnchorClicked(val) {
    console.log("onTooltipAnchorClicked", val);
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
