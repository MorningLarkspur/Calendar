<script setup>
import { ref } from 'vue'
import ModalObj from './ModalObj.vue'
import AppButton from './AppButton.vue'

const props = defineProps({
  data: Array,
  columns: Array,
  inputDate: Date,
  monthList: Array,
  weekdays: Array
})

const emit = defineEmits(['added', 'change-select'])
const selectedDate = ref(props.inputDate.getDate())
const eventUnit = ref()
const showAddEvent = ref(false)
const eventTitle = ref('')
const eventDate = ref(props.inputDate)
const eventHour = ref(12)
const eventMin = ref('00')
const eventSuffix = ref('AM')

function select(d) {
  if (d != '') {
    selectedDate.value = d
    eventDate.value.setDate(selectedDate.value)
    emit('change-select', selectedDate)
  }
}
function showModal(date) {
  select(date)
  showAddEvent.value = true
}

function addEvent(eTitle, eDate, eHour, eMin, eSuffix) {
  if (eTitle == '') {
    alert('Title field empty. Event cannot be created.')
  } else {
    if (eSuffix == 'AM') {
      if (eHour == 12) {
        eDate.setHours(0, Number(eMin))
      } else {
        eDate.setHours(eHour, Number(eMin))
      }
    } else {
      if (eHour == 12) {
        eDate.setHours(12, Number(eMin))
      } else {
        eDate.setHours(Number(eHour) + 12, Number(eMin))
      }
    }
    eventUnit.value = { Title: eTitle, Date: eDate }
    emit('added', eventUnit.value)
    closeModal()
  }
}

function closeModal() {
  showAddEvent.value = false
  eventTitle.value = ''
}
</script>

<template>
  <table class="border-2 border-black rounded-[3px] bg-stone-100">
    <thead>
      <tr>
        <th
          v-for="weekday in columns"
          :key="weekday.id"
          class="border-[1px] border-black bg-amber-700 text-white size-14 text-xl font-bold"
        >
          {{ weekday.toUpperCase() }}
        </th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="day in data" :key="day.id">
        <td
          v-for="weekday in columns"
          :key="weekday.id"
          @click="select(day[weekday])"
          :class="{ 'bg-orange-300': selectedDate == day[weekday] && selectedDate != '' }"
          class="relative border-[1px] border-black bg-amber-50 text-black size-14"
        >
          <button
            v-if="day[weekday] != ''"
            @click="showModal(day[weekday])"
            class="absolute top-[-4px] right-1"
          >
            +
          </button>
          <p class="text-center text-xl font-bold">{{ day[weekday] }}</p>
        </td>
      </tr>
    </tbody>
    <Teleport to="body">
      <ModalObj :show="showAddEvent">
        <template #header>
          <h3>Add Event</h3>

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
          <AppButton
            class="float-right text-black border border-black rounded-md mb-2 mr-2 px-1"
            @click="closeModal()"
            >Cancel</AppButton
          >
          <AppButton
            class="float-right text-black border border-black rounded-md mb-2 mr-2 px-1"
            @click="addEvent(eventTitle, eventDate, eventHour, eventMin, eventSuffix)"
            >Add</AppButton
          >
        </template>
      </ModalObj>
    </Teleport>
  </table>
</template>
