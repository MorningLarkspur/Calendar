<script setup>
import { ref } from 'vue'
import AppButton from './AppButton.vue'
import CountDown from './CountDown.vue'
import ModalObj from './ModalObj.vue'

const props = defineProps({
  eventsList: Array,
  show: Boolean,
  monthList: Array,
  weekdays: Array
})
const emit = defineEmits(['deleted', 'updated'])
const showEditEvent = ref(false)
const eventTitle = ref()
const eventIndex = ref()
const eventHour = ref(12)
const eventMin = ref('00')
const eventSuffix = ref('AM')
const eventDate = ref()

function openEditModal(inputEvent, inputIndex) {
  showEditEvent.value = true
  eventTitle.value = inputEvent.Title
  eventIndex.value = inputIndex
  eventDate.value = inputEvent.Date
  eventMin.value = String(eventDate.value.getMinutes()).padStart(2, '0')
  if (inputEvent.Date.getHours() < 12) {
    if (inputEvent.Date.getHours() == 0) {
      eventHour.value = 12
    } else {
      eventHour.value = eventDate.value.getHours()
    }
    eventSuffix.value = 'AM'
  } else {
    if (inputEvent.Date.getHours() != 12) {
      eventHour.value = eventDate.value.getHours() - 12
    }
    eventSuffix.value = 'PM'
  }
}
function closeModal() {
  showEditEvent.value = false
}
function editEvent(index, title, hour, min, suffix, date) {
  if (title == '') {
    alert('Title field empty. Event cannot be updated')
  } else {
    date.setMinutes(Number(min))
    if (suffix == 'AM') {
      if (hour == 12) {
        date.setHours(0)
      } else {
        date.setHours(hour)
      }
    } else {
      if (hour == 12) {
        date.setHours(12)
      } else {
        date.setHours(Number(hour) + 12)
      }
    }
    emit('updated', [index, title, date])
    closeModal()
  }
}
</script>

<template>
  <div
    v-if="show"
    class="flex flex-col fixed top-[510px] bottom-0 left-0 w-full bg-slate-800 text-white h-auto"
  >
    <TransitionGroup tag="ul" name="fade" class="h-dvh overflow-y-auto p-0 list-none">
      <li
        v-for="(event, index) in props.eventsList"
        class="basis-1/4 w-full h-auto bg-lime-50 text-black border-2 border-black box-border pl-15 list-none"
        :key="index"
      >
        <AppButton
          @click="openEditModal(event, index)"
          class="float-right text-black border border-black rounded-md mt-3 mr-3 px-1"
        >
          Edit
        </AppButton>
        <div v-if="event.Date > new Date()" class="float-right mr-3 mt-3 text-lg font-bold">
          <CountDown :goal="event.Date"></CountDown>
        </div>
        <div v-else class="float-right mr-3 mt-3 text-lg font-bold">Done</div>
        <h2 class="text-2xl font-bold m-2">{{ event.Title }}</h2>
        <button
          @click="$emit('deleted', index)"
          class="float-right text-xs text-black border border-black rounded-md mr-3 px-1"
        >
          X
        </button>
        <p class="ml-2 text-lg">{{ event.Date }}</p>
      </li>
    </TransitionGroup>
    <Teleport to="body">
      <ModalObj :show="showEditEvent">
        <template #header>
          <h3>Edit Event</h3>
          <h3 class="absolute top-5 right-5">
            {{
              weekdays[eventDate.getDay()] +
              ', ' +
              monthList[eventDate.getMonth()] +
              ' ' +
              eventDate.getDate() +
              ' ' +
              eventDate.getFullYear()
            }}
          </h3>
        </template>
        <template #body>
          <input v-model="eventTitle" placeholder="Title" class="border border-gray-300" />
          <select v-model="eventHour">
            <option v-for="n in 12" :key="n.id">{{ n }}</option>
          </select>
          <select v-model="eventMin">
            <option v-for="n in 12" :key="n.id">{{ String((n - 1) * 5).padStart(2, '0') }}</option>
          </select>
          <select v-model="eventSuffix">
            <option>AM</option>
            <option>PM</option>
          </select>
        </template>
        <template #footer>
          <button
            @click="closeModal()"
            class="float-right text-black border border-black rounded-md mb-2 mr-2 px-1"
          >
            Cancel
          </button>
          <button
            @click="editEvent(eventIndex, eventTitle, eventHour, eventMin, eventSuffix, eventDate)"
            class="float-right text-black border border-black rounded-md mb-2 mr-2 px-1"
          >
            Update
          </button>
        </template>
      </ModalObj>
    </Teleport>
  </div>
</template>
