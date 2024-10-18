<script setup>
import { ref, computed } from 'vue'
import AppButton from '@/components/AppButton.vue'
import SelectMenu from '@/components/SelectMenu.vue'
import CalendarGrid from '@/components/CalendarGrid.vue'
import EventPane from '@/components/EventPane.vue'

const gridKey = ref(0)
const updateCalendar = () => {
  gridKey.value += 1
  if (gridKey.value == 100) {
    gridKey.value = 0
  }
}
const monthList = [
  'January',
  'February',
  'March',
  'April',
  'May',
  'June',
  'July',
  'August',
  'September',
  'October',
  'November',
  'December'
]
const monthLength = {
  January: 31,
  February: 28,
  March: 31,
  April: 30,
  May: 31,
  June: 30,
  July: 31,
  August: 31,
  September: 30,
  October: 31,
  November: 30,
  December: 31
}
const date = new Date()
const showMonth = ref(false)
const showYear = ref(false)
const currentMonth = monthList[date.getMonth()]
const selectedMonth = ref(currentMonth)
const currentYear = date.getFullYear()
const selectedYear = ref(currentYear)
const currentDate = date.getDate()
const selectedDate = ref(currentDate)
const weekdays = ['Sun', 'Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat']
const week = { Sun: '', Mon: '', Tue: '', Wed: '', Thu: '', Fri: '', Sat: '' }
const eventList = ref([])
const yearList = [...Array(currentYear + 9).keys()].slice(currentYear - 11)

function populateMonth(month, year) {
  let dates = []
  let tempMonLen = monthLength[month]
  if (month == 'February' && year.value % 400 == 0) {
    tempMonLen++
  } else if (month == 'February' && year.value % 4 == 0 && !(year.value % 100 == 0)) {
    tempMonLen++
  }
  for (let i = 1; i <= tempMonLen; i++) {
    let tempDate = new Date(year, monthList.indexOf(month), i)
    let day = tempDate.getDay()
    if (i == 1 && day > 0) {
      switch (day) {
        case 1:
          week['Sun'] = ''
          break
        case 2:
          week['Sun'] = ''
          week['Mon'] = ''
          break
        case 3:
          week['Sun'] = ''
          week['Mon'] = ''
          week['Tue'] = ''
          break
        case 4:
          week['Sun'] = ''
          week['Mon'] = ''
          week['Tue'] = ''
          week['Wed'] = ''
          break
        case 5:
          week['Sun'] = ''
          week['Mon'] = ''
          week['Tue'] = ''
          week['Wed'] = ''
          week['Thu'] = ''
          break
        case 6:
          week['Sun'] = ''
          week['Mon'] = ''
          week['Tue'] = ''
          week['Wed'] = ''
          week['Thu'] = ''
          week['Fri'] = ''
          break
      }
    }
    switch (day) {
      case 0:
        week['Sun'] = i
        break
      case 1:
        week['Mon'] = i
        break
      case 2:
        week['Tue'] = i
        break
      case 3:
        week['Wed'] = i
        break
      case 4:
        week['Thu'] = i
        break
      case 5:
        week['Fri'] = i
        break
      case 6:
        week['Sat'] = i
        dates.push({
          Sun: week['Sun'],
          Mon: week['Mon'],
          Tue: week['Tue'],
          Wed: week['Wed'],
          Thu: week['Thu'],
          Fri: week['Fri'],
          Sat: week['Sat']
        })
        break
    }
    if (i == tempMonLen && day < 6) {
      switch (day) {
        case 0:
          week['Mon'] = ''
          week['Tue'] = ''
          week['Wed'] = ''
          week['Thu'] = ''
          week['Fri'] = ''
          week['Sat'] = ''
          break
        case 1:
          week['Tue'] = ''
          week['Wed'] = ''
          week['Thu'] = ''
          week['Fri'] = ''
          week['Sat'] = ''
          break
        case 2:
          week['Wed'] = ''
          week['Thu'] = ''
          week['Fri'] = ''
          week['Sat'] = ''
          break
        case 3:
          week['Thu'] = ''
          week['Fri'] = ''
          week['Sat'] = ''
          break
        case 4:
          week['Fri'] = ''
          week['Sat'] = ''
          break
        case 5:
          week['Sat'] = ''
          break
      }
      dates.push({
        Sun: week['Sun'],
        Mon: week['Mon'],
        Tue: week['Tue'],
        Wed: week['Wed'],
        Thu: week['Thu'],
        Fri: week['Fri'],
        Sat: week['Sat']
      })
    }
  }
  for (; dates.length < 6; ) {
    dates.push({
      Sun: '',
      Mon: '',
      Tue: '',
      Wed: '',
      Thu: '',
      Fri: '',
      Sat: ''
    })
  }
  return dates
}

function changeMonth(forward, month) {
  if (forward && month != null) {
    selectedDate.value = 1
    selectedMonth.value = month
    showMonth.value = false
  } else {
    if (forward) {
      if (monthList.indexOf(selectedMonth.value) < 11) {
        let tempIndex = monthList.indexOf(selectedMonth.value) + 1
        selectedMonth.value = monthList[tempIndex]
        selectedDate.value = 1
      } else if (selectedYear.value != currentYear + 8) {
        selectedMonth.value = monthList[0]
        selectedYear.value++
        selectedDate.value = 1
      }
    } else {
      if (monthList.indexOf(selectedMonth.value) > 0) {
        let tempIndex = monthList.indexOf(selectedMonth.value) - 1
        selectedMonth.value = monthList[tempIndex]
        selectedDate.value = monthLength[selectedMonth.value]
      } else if (selectedYear.value != currentYear - 11) {
        selectedMonth.value = monthList[11]
        selectedYear.value--
        selectedDate.value = monthLength[selectedMonth.value]
      }
    }
  }
  updateCalendar()
}
function changeYear(year) {
  selectedDate.value = 1
  selectedYear.value = year
  showYear.value = false
  updateCalendar()
}
function reset() {
  selectedMonth.value = currentMonth
  selectedYear.value = currentYear
  selectedDate.value = currentDate
  updateCalendar()
}
function addNewEvent(eventToAdd) {
  eventList.value.push(eventToAdd)
  updateCalendar()
}
function removeEvent(eventToRemove) {
  eventList.value.splice(eventToRemove, 1)
  updateCalendar()
}
function updateEvent(eventToUpdate) {
  eventList.value[eventToUpdate[0]].Title = eventToUpdate[1]
  eventList.value[eventToUpdate[0]].Date = new Date(eventToUpdate[2])
  console.log(eventList.value[eventToUpdate[0]].Date)
  updateCalendar()
}
const chronoEvents = computed(() => {
  let events = eventList.value
  events.sort((a, b) => a.Date - b.Date)
  return events
})
</script>

<template>
  <div class="absolute top-10 left-10 text-3xl font-bold">
    <AppButton @click="showMonth = true">
      {{ selectedMonth }}
    </AppButton>
    {{ ' ' }}
    <AppButton @click="showYear = true"> {{ selectedYear }}</AppButton>
  </div>
  <div class="absolute top-10 right-10">
    <AppButton @click="changeMonth(false)" class="pr-3"> {{ '<' }}</AppButton>
    <AppButton @click="reset()" class="px-3">Today</AppButton>
    <AppButton @click="changeMonth(true)" class="pl-3">
      {{ '>' }}
    </AppButton>
  </div>
  <CalendarGrid
    :data="populateMonth(selectedMonth, selectedYear)"
    :columns="weekdays"
    :inputDate="new Date(selectedYear, monthList.indexOf(selectedMonth), selectedDate)"
    :monthList="monthList"
    :weekdays="weekdays"
    :key="gridKey"
    @change-select="
      (newDate) => {
        selectedDate = newDate.value
      }
    "
    @added="
      (newEvent) => {
        addNewEvent(newEvent)
      }
    "
    class="absolute top-[85px] left-[40px]"
  ></CalendarGrid>

  <Teleport to="body">
    <SelectMenu
      :type="'month'"
      :show="showMonth"
      :content="monthList"
      :currentSelected="selectedMonth"
      @close="showMonth = false"
      @select="(option) => changeMonth(true, option)"
    ></SelectMenu>
    <SelectMenu
      :type="'year'"
      :show="showYear"
      :content="yearList"
      :currentSelected="selectedYear"
      @close="showYear = false"
      @select="(option) => changeYear(option)"
    ></SelectMenu>
  </Teleport>
  <EventPane
    id="eventPane"
    :eventsList="chronoEvents"
    :show="true"
    :monthList="monthList"
    :weekdays="weekdays"
    @deleted="(eventToBeRemoved) => removeEvent(eventToBeRemoved)"
    @updated="(updatedEvent) => updateEvent(updatedEvent)"
  >
  </EventPane>
</template>
