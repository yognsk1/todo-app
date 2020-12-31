<template>
  <div id="fullCalendar">
    <full-calendar
      @event-selected="eventSelected"
      @day-click="dayClicked"
      @event-created="selectedMultipleTime"
      @render-event="renderEvent"
      :events="events"
      :config="config"
      :eventLimit="true"
    />
  </div>
</template>

<script>
import Vue from "vue";
import Component from "vue-class-component";
import { FullCalendar } from "vue-full-calendar";
//import { ScheduleEvent } from "@/domain/ScheduleEvent";
import tippy from "tippy.js";
//import { FullCalendarToolTipHTMLText } from "@/helper/FullCalendarToolTipRenderer";

@Component({
  components: {
    FullCalendar,
  },
  props: {
    scheduleEventList: Array,
    specialtyLocation: Object,
  },
})
class FullCalendarComponent extends Vue {
  scheduleEventList;
  specialtyLocation;
  monthNames = [
    "Jan",
    "Feb",
    "Mar",
    "Apr",
    "May",
    "Jun",
    "Jul",
    "Aug",
    "Sept",
    "Oct",
    "Nov",
    "Dec",
  ];
  timeEvent = "";
  config = {
    timeZone: "local",
    editable: false,
    selectable: true,
    nowIndicator: false,
    fixedWeekCount: false,
    handleWindowResize: true,
    eventLimit: true,
    eventLimitText: "",
    views: {
      month: {
        eventLimit: 2,
        titleFormat: "MMMM, YYYY",
      },
      agendaWeek: {
        eventLimit: 2,
        titleFormat: "D MMM",
        allDayText: "All Day",
      },
      agenda: {
        eventLimit: 2,
        titleFormat: "dddd D MMM",
        allDayText: "All Day",
      },
    },
    header: {
      left: "title,prev,next",
      center: "",
      right: "agendaDay,agendaWeek,month,listWeek",
    },

    bootstrapFontAwesome: {
      prev: "fa-chevron-left",
      next: "fa-chevron-right",
    },
    footer: {
      left: "",
      center: "addEvent",
      right: "",
    },
    defaultView: "month",
    buttonText: {
      listWeek: "List",
      month: "Monthly",
      agendaWeek: "Weekly",
      agendaDay: "Daily",
    },
    eventAfterRender: this.eventAfterRenderListener,
    eventAfterAllRender: this.eventAfterAllRender,
    fullDay: false,
  };

  eventAfterRenderListener() {
    /*const selectedLocation = "this.$store.state.selectedLocation";
    let locationName = "";
    if (!selectedLocation) {
      locationName = "";
    } else {
      locationName = selectedLocation.name;
    }

    const htmlText = new FullCalendarToolTipHTMLText(
      event,
      locationName,
      "this.$store.state.selectedSpecialty.name"
    );
    element.html(htmlText.text);*/
  }

  eventAfterAllRender() {
    tippy("div.tooltipElement", {
      theme: "light-border",
      placement: "top-start",
      content(reference) {
        const id = reference.getAttribute("data-template");
        const container = document.createElement("div");
        const linkedTemplate = document.getElementById(id);
        const node = document.importNode(linkedTemplate.content, true);
        container.appendChild(node);
        return container;
      },
    });

    // @ts-ignore
    /*jQuery("a.tooltipStaffLinkAnchor").click(function(jsEvent) {
      jsEvent.preventDefault();
      // @ts-ignore
      const self = jQuery(this);
      const staffId = self.data("staffid");
      const eventValue = {
        jsEvent,
        staffId,
      };

      vueComponent.$emit("tooltipAnchorClicked", eventValue);
    });*/
  }

  get selectedHospitalName() {
    return "this.$store.state.selectedLocation.name";
  }

  get selectedSpecialtyName() {
    return "this.$store.state.selectedSpecialty";
  }

  beforeUpdate() {
    // this.convertScheduleEventListToEvents();
  }

  getCurrentTimeFromTimeZone(date, tzString) {
    return new Date(
      (typeof date === "string" ? new Date(date) : date).toLocaleString(
        "en-US",
        { timeZone: tzString }
      )
    );
  }

  get events() {
    return this.scheduleEventList.map((scheduleEvent) => {
      const utcStart = new Date(scheduleEvent.startTime);
      const utcEnd = new Date(scheduleEvent.endTime);

      // Convert to UTC equivalent dates.
      const startDate = new Date(
        utcStart.getTime() + utcStart.getTimezoneOffset() * 60000
      );
      const endDate = new Date(
        utcEnd.getTime() + utcEnd.getTimezoneOffset() * 60000
      );

      let color;
      let bordercolor;
      const start =
        startDate.getFullYear() +
        "-" +
        startDate.getMonth() +
        "-" +
        startDate.getDate();
      const end =
        endDate.getFullYear() +
        "-" +
        endDate.getMonth() +
        "-" +
        endDate.getDate();

      if (this.$store.state.selectedLocation) {
        const timeAtTimeZone = this.getCurrentTimeFromTimeZone(
          new Date(),
          "this.$store.state.selectedLocation.locationAddressDetail.timeZone.name"
        );

        if (timeAtTimeZone.getTime() > endDate.getTime()) {
          color = "#F2F2F2";
          bordercolor = "#de4138";
        } else {
          color = "#edf6e5";
          bordercolor = "#71be3c";
        }
      }

      // note: as this library considers endtime is exclusive. more information here
      // https://fullcalendar.io/docs/event-object <-- go to end property documentation.
      // That's why I need to increase end-time with one more day
      // Change here also needs to be reflected on the tooltip so one previous date is used in tooltip.
      // We will not change end date if event is on the same date
      if (start !== end && endDate.getHours() < 9) {
        endDate.setDate(endDate.getDate() + 1);
      }

      return {
        // title: scheduleEvent.name,
        start: startDate,
        end: endDate,
        borderColor: bordercolor,
        doctors: [
          scheduleEvent.staff.firstName + " " + scheduleEvent.staff.lastName,
        ],
        notes: scheduleEvent.notes,
        allDay: false,
        backgroundColor: color,
        _target: scheduleEvent,
        eventColor: "#ffffff",
      };
    });
  }

  eventSelected(args, jsEvent) {
    if (jsEvent.target.className === "tooltipStaffLinkAnchor") {
      return;
    }

    const eventValue = {
      event: "eventSelected",
      value: {
        args,
      },
    };
    this.$emit("calanderEventItemClicked", eventValue);
  }

  dayClicked(date, jsEvent, view) {
    const eventValue = {
      value: {
        arg1: date,
        arg2: jsEvent,
        arg3: view,
      },
      event: "dayClicked",
    };

    this.$emit("calanderEventItemClicked", eventValue);
  }

  selectedMultipleTime(arg) {
    const eventValue = {
      value: {
        arg,
      },
      event: "timeSlotSelected",
    };
    this.$emit("calanderEventItemClicked", eventValue);
  }

  renderEvent() {
    //
  }

  mouseHover() {
    //
  }
}

export default FullCalendarComponent;
</script>

<style>
#fullCalendar .tooltip-inner {
  max-width: none;
  white-space: nowrap;
  background: white;
  border: 1px solid lightgray;
  -webkit-box-shadow: 0px 3px 3px 0px rgba(0, 0, 0, 0.3);
  -moz-box-shadow: 0px 3px 3px 0px rgba(0, 0, 0, 0.3);
  box-shadow: 0px 3px 3px 0px rgba(0, 0, 0, 0.3);
  color: gray;
  margin: 0;
  padding: 0;
}

#fullCalendar .tooltip.bottom .tooltip-arrow {
  top: 0;
  left: 50%;
  margin-left: -5px;
  border-bottom-color: lightgray; /* black */
  border-width: 0 5px 5px;
}

#fullCalendar .fc-event {
  border-right: 0px !important;
  border-top: 0px !important;
  border-bottom: 0px !important;
}

#fullCalendar .fc button {
  margin-left: 5px !important;
  font-size: 12px !important;
  height: 25px !important;
}

#fullCalendar .fc-toolbar.fc-header-toolbar {
  vertical-align: middle !important;
  padding-bottom: 10px !important;
  margin-bottom: 0px !important;
}

#fullCalendar .fc-footer-toolbar {
  display: none;
}

#fullCalendar h2 {
  font-size: 20px !important;
  vertical-align: middle !important;
}

#fullCalendar .fc-state-active {
  background: #de4138 !important;
  color: white !important;
  border-radius: 20px !important;
}

#fullCalendar .fc-state-default {
  background: #f2f2f2;
  color: #4c5764;
  border-radius: 20px !important;
}

#fullCalendar .fc-next-button {
  background: #f2f2f2 !important;
  color: #4c5865 !important;
  border-radius: 100% !important;
  text-align: left !important;
  padding-left: 0px !important;
  width: 25px !important;
}

#fullCalendar .fc-icon {
  font-size: 15px !important;
}

#fullCalendar .fc-prev-button {
  background: #f2f2f2 !important;
  color: #4c5865 !important;
  border-radius: 100% !important;
  width: 25px !important;
  padding-left: 0px !important;
}

#fullCalendar .fc-axis.fc-widget-header {
  width: 58px !important;
}

#fullCalendar .fc-axis.fc-widget-content {
  padding: 5px !important;
  text-align: center !important;
}

#fullCalendar .fc-day-header.fc-widget-header {
  border: none !important;
}

#fullCalendar .fc-list-heading-main {
  padding: 1%;
}

#fullCalendar .fc-list-item .tooltipElement {
  margin: 2px;
  width: 15%;
  border: 1.5px solid yellowgreen;
  border-radius: 5px;
  padding: 5px;
}

#fullCalendar .fc-list-item .tooltipElement.olderEvent {
  border: none;
  border: 1.5px solid grey;
}

#fullCalendar .fc-list-heading-alt {
  margin-right: 1%;
  margin-top: 0.7%;
  vertical-align: center !important;
}

#fullCalendar .fc-list-item-time.fc-widget-content {
  padding-left: 1% !important;
}

#fullCalendar .fc-list-item-title.fc-widget-content {
  padding-left: 2% !important;
}

#fullCalendar .fc-event-dot {
  margin-left: 150% !important;
}

#fullCalendar .fc-time-grid-event.fc-v-event.fc-event.fc-start.fc-end {
  margin-left: 1% !important;
  padding: 5px !important;
}

#fullCalendar .fc-day-grid-event.fc-h-event.fc-event.fc-start.fc-end {
  margin-left: 1.5% !important;
  padding: 5px !important;
}

#fullCalendar td.fc-event-container {
  margin-left: 100% !important;
  padding-left: 0% !important;
}

#fullCalendar .fc-list-item {
  border: 1px solid #aaaaaa;
  padding: 5px !important;
}

#fullCalendar .tooltip {
  position: relative !important;
  display: inline-block;
  opacity: 1 !important;
}

#fullCalendar .tooltip .tooltiptext {
  visibility: hidden;
  width: 400px;
  height: auto;
  background-color: white;
  color: black;
  text-align: center;
  border-radius: 6px;
  padding: 5px;
  position: absolute;
  z-index: 1;
  bottom: 100%;
  left: 50%;
  margin-left: -60px;
}

#fullCalendar .tooltip .tooltiptext::after {
  content: "";
  position: absolute;
  top: 100%;
  left: 50%;
  margin-left: -5px;
  border-width: 5px;
  border-style: solid;
  border-color: #aaaaaa transparent transparent transparent;
}

#fullCalendar .tooltip:hover .tooltiptext {
  visibility: visible;
  border: 1px solid #aaaaaa;
}

#fullCalendar .fc-day-header.fc-widget-header {
  font-size: 12px;
  padding: 3px;
}

#fullCalendar .fc-other-month.fc-past {
  background: #f2f2f2;
}

#fullCalendar .fc-other-month.fc-future {
  background: #f2f2f2;
}

#fullCalendar .fc-day-number {
  float: left !important;
  font-size: 13px;
}

#fullCalendar .fc-day-grid-event.fc-h-event.fc-event {
  padding: 3px !important;
  border-left: 2.5px solid;
  border-radius: 0px;
}

#fullCalendar .fc-time-grid-event.fc-v-event.fc-event {
  padding: 3px !important;
  border-left: 2.5px solid;
  border-radius: 0px;
}

#fullCalendar .fc-today {
  background: #fff !important;
  border: none !important;
  border-top: 1px solid #ddd !important;
  border-left: 1px solid #ddd !important;
  font-weight: bold;
}

#fullCalendar .fc-day-top.fc-today > span {
  color: white;
  margin: 1%;
  padding-left: 6px !important;
  padding-right: 0px !important;
  background: #de4138;
  border-radius: 100%;
  width: 20px;
  height: 20px;
}

#fullCalendar .fc .fc-row .fc-content-skeleton td,
#fullCalendar .fc .fc-row .fc-helper-skeleton td {
  border-right: 1px solid #ddd !important;
}

#fullCalendar .fc-day-header.fc-widget-header.fc-today {
  color: white !important;
  background: #de4138 !important;
  border-radius: 15px !important;
  font-size: 12px !important;
  padding: 3px !important;
}

#fullCalendar .fc-more {
  border: 2px solid #1c6dc8;
  border-radius: 15px;
  padding-left: 5px;
  padding-right: 5px;
  font-size: 12px;
  background: #1c6dc8;
  color: white !important;
  font-weight: 700;
}

#fullCalendar .doctorNameInHoverModal {
  font-size: 10px;
  float: right;
  width: fit-content;
  border-radius: 20px;
  border: 1px solid #aaaaaa;
  background: white;
  padding-left: 10px;
  padding-right: 10px;
  margin: 1% !important;
}
</style>
