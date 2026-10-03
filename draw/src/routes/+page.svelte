<ExampleShell demo="Draw events">
    <ScheduleXCalendar calendarApp={calendarApp} />
</ExampleShell>

<script lang="ts">
  import { ScheduleXCalendar } from '@schedule-x/svelte';
  import { createCalendar, createViewDay, createViewWeek, createViewMonthGrid } from '@schedule-x/calendar';
  import '@schedule-x/theme-default/dist/index.css';
  import {createDrawPlugin} from "@sx-premium/draw";
  import ExampleShell from '$lib/ExampleShell.svelte';
  import '../app.css';
  import 'temporal-polyfill/global';

  const drawPlugin = $state(createDrawPlugin({
    onFinishDrawing: (event) => {
      console.log('onFinishDrawing', event);
    },

    snapDuration: 30
  }));

  const calendarApp = $state(createCalendar({
    views: [
      createViewDay(),
      createViewWeek(),
      createViewMonthGrid()
    ],

    plugins: [
      drawPlugin
    ],

    callbacks: {
      onMouseDownDateTime: (dateTime, mouseDownEvent) => {
        drawPlugin.drawTimeGridEvent(dateTime, mouseDownEvent, {
          title: 'New Event',
        })
      },

      onMouseDownDateGridDate: (dateTime, mouseDownEvent) => {
        drawPlugin.drawDateGridEvent(dateTime, mouseDownEvent, {
          title: 'New Event',
        })
      },

      onMouseDownMonthGridDate: (dateTime) => {
        drawPlugin.drawMonthGridEvent(dateTime, {
          title: 'New Event',
        })
      }
    }
  }))
</script>
