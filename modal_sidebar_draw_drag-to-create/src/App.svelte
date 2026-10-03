<ExampleShell demo="Modal, sidebar, draw, and drag to create">
  <ScheduleXCalendar calendarApp={calendarApp} />
</ExampleShell>

<script lang="ts">
  import { ScheduleXCalendar } from '@schedule-x/svelte';
  import { createCalendar, createViewDay, createViewWeek, createViewMonthGrid } from '@schedule-x/calendar';
  import {createInteractiveEventModal} from "@sx-premium/interactive-event-modal";
  import {createEventsServicePlugin} from "@schedule-x/event-recurrence";
  import {createSidebarPlugin} from "@sx-premium/sidebar";

  import '@schedule-x/theme-default/dist/index.css';
  import '@sx-premium/interactive-event-modal/index.css';
  import '@sx-premium/sidebar/index.css'
  import '@sx-premium/drag-to-create/index.css'
  import {createDragToCreatePlugin} from "@sx-premium/drag-to-create";
  import {createDrawPlugin} from "@sx-premium/draw";
  import ExampleShell from './ExampleShell.svelte';
  import 'temporal-polyfill/global';

  const eventsService = $state(createEventsServicePlugin());

  const modalPlugin = $state(createInteractiveEventModal({
    onAddEvent: (event) => {
      console.log('onAddEvent', event);
    },

    eventsService
  }));

  const dragToCreate = $state(createDragToCreatePlugin({
    onAddEvent: (event) => {
      console.log('onAddEvent', event);
    },
  }))

  const drawPlugin = $state(createDrawPlugin({
    onFinishDrawing: (event) => {
      console.log('onFinishDrawing', event);
    }
  }))

  const sidebarPlugin = $state(createSidebarPlugin({
    isPlaceholderEventSelectable: true,
    placeholderEvents: [
      {
        title: 'Event 1',
        calendarId: 'personal'
      },
      {
        title: 'Event 2',
        calendarId: 'work'
      }
    ],

    activeCalendarIds: ['personal', 'work'],

    eventsService
  }));

  const calendarApp = $state(createCalendar({
    views: [
      createViewDay(),
      createViewWeek(),
      createViewMonthGrid()
    ],

    plugins: [
      modalPlugin,
      eventsService,
      sidebarPlugin,
      drawPlugin,
      dragToCreate,
    ],

    callbacks: {
      onDoubleClickDateTime: (dateTime) => {
        modalPlugin.clickToCreate(dateTime);
      },

      onMouseDownDateTime: (dateTime, mouseDownEvent) => {
        if (!sidebarPlugin.selectedPlaceholderEvent.value) return;

        drawPlugin.drawTimeGridEvent(
          dateTime,
          mouseDownEvent,
          {
            ...sidebarPlugin.selectedPlaceholderEvent.value
          }
        )
      },

      onMouseDownDateGridDate: (date, mouseDownEvent) => {
        if (!sidebarPlugin.selectedPlaceholderEvent.value) return;

        drawPlugin.drawDateGridEvent(
          date,
          mouseDownEvent,
          {
            ...sidebarPlugin.selectedPlaceholderEvent.value
          }
        )
      }
    },

    calendars: {
      personal: {
        label: 'Personal',
        colorName: 'personal',
        lightColors: {
          main: '#f8bbd0',
          container: '#fce4ec',
          onContainer: '#000',
        }
      },
      work: {
        label: 'Work',
        colorName: 'work',
        lightColors: {
          main: '#90caf9',
          container: '#e3f2fd',
          onContainer: '#000',
        }
      }
    }
  }))
</script>
