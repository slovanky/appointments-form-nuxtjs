<script setup>
import { onMounted, ref } from 'vue'
import { useRoute } from 'vue-router'

import axios from 'axios'

import { IconArrowLeft, IconCalendarEventFilled, IconChecks, IconClockHour3, IconUser } from '@tabler/icons-vue'

const route = useRoute()
const resourceId = route.params.resourceId
const typeId = route.params.typeId

const dateList = ref([])
const currentResource = ref({})
const currentType = ref({})

const selectedDate = ref({})
const timeList = ref([])
const selectedTime = ref({})

const formIsOpen = ref(false)

onMounted(() => {
  fetchDateList()
  fetchResource()
})

// Feching Date List
async function fetchDateList() {
  try {
    const response = await axios.post(
      'https://staging.ehjzny.com/api/v1/availability/times',
      {
        "type": typeId, // required,
        "resourceFilters": {
          "resource": resourceId,
        }
      },
      {
        headers: {
          'X-APP-ID': 'h66w3DSEcsKlu8ReaclX'
        }
      }
    )

    dateList.value = response.data?.result || []

    console.log(response.data?.result);

  } catch (error) {
    console.error('Fetching Time List Error:', error)
  }
}

// Fetching Current Resource
async function fetchResource() {
  try {
    const response = await axios.get(
      `https://staging.ehjzny.com/api/v1/availability/resources/${resourceId}`,
      {
        headers: {
          'X-APP-ID': 'h66w3DSEcsKlu8ReaclX'
        }
      }
    )

    currentResource.value = response.data?.result || {}
    currentType.value = getCurrentTypeById(response.data?.result?.types, typeId)

    console.log('Resource: ', response.data?.result);

  } catch (error) {
    console.error('Fetching Resource Error:', error)
  }
}

function setCurentTimes(date) {
  dateList.value.find(item => {
    if (item.date === date.date) {
      selectedDate.value = date
      timeList.value = item.times
      selectedTime.value = {}
    }
  })
}

function setSelectedTime(time) {
  selectedDate.value?.times.find(item => {
    if (item.time === time.time) {
      selectedTime.value = time
      formIsOpen.value = true
    }
  })
}

function getCurrentTypeById(typeList, typeId) {
  return typeList.find(type => type.id === typeId)
}
</script>

<template>
  <div :class="formIsOpen ? 'right-0' : '-right-full'" class="z-[99] fixed top-0 h-screen w-[480px] bg-white transition-main">
    <div class="px-5 pt-5 flex items-center gap-x-3">
      <button :onclick="() => formIsOpen = false" class="w-6 h-6 flex items-center justify-center border border-zinc-300 rounded-sm cursor-pointer">
        <IconArrowLeft size="20" stroke="2" />
      </button>
      <div class="pe-8 grow text-center font-bold">Confirm</div>
    </div>

    <div class="p-5 space-y-5">
      <div class="p-3 space-y-2 bg-[#F4F4F4] border border-zinc-300 rounded">
        <div class="flex items-center gap-x-3">
          <IconUser size="20" stroke="2" />
          <span>{{ currentResource?.name }}</span>
        </div>
        <div class="flex items-center gap-x-3">
          <IconChecks size="20" stroke="2" />
          <span class="capitalize">{{ currentType?.name }} <span class="text-sm">({{ currentType?.minutes }} min)</span></span>
        </div>
        <div class="flex items-center gap-x-3">
          <IconCalendarEventFilled size="20" stroke="2" />
          <span>{{ selectedDate?.display }}</span>
        </div>
        <div class="flex items-center gap-x-3">
          <IconClockHour3 size="20" stroke="2" />
          <span>{{ selectedTime?.display }}</span>
        </div>
      </div>

      <AppointmentFrom />
    </div>
  </div>
  <div v-if="formIsOpen" :onclick="() => formIsOpen = false" class="z-[98] fixed top-0 right-0 h-screen w-screen bg-black/60 backdrop-blur transition"></div>

  
  <div class="grow flex items-center justify-center">
    <div class="wrapper h-max">
      <div class="w-full">
        <h2 class="text-lg text-center font-semibold mb-4">Available Times</h2>

        <div class="h-[480px] grid grid-cols-3 divide-x divide-black/10 bg-white border border-[#D6D7D9] rounded-lg shadow overflow-hidden">
          <!-- Resource & Type [Column] -->
          <div class="p-5 overflow-y-auto">
            <div class="space-y-5">
              <div v-if="currentResource?.name" class="flex items-center gap-x-2">
                <div class="w-8 h-8 aspect-square shrink-0 flex items-center justify-center bg-zinc-100 rounded-full">
                  <IconUser size="18" stroke="2" class="text-zinc-500" />
                </div>
                <h4 class="text-sm font-bold">{{ currentResource?.name }}</h4>
              </div>
              <!-- Skeleton Loader -->
              <div v-else class="flex items-center gap-x-2">
                <div class="w-8 h-8 aspect-square shrink-0 bg-black/5 rounded-full animate-pulse"></div>
                <div class="h-3 w-1/2 bg-black/5 rounded animate-pulse"></div>
              </div>

              <div v-if="currentType?.name" class="space-y-2">
                <h3 class="text-xl font-bold capitalize">{{ currentType?.name }}</h3>
                <div class="flex items-center gap-x-2">
                  <IconClockHour3 size="20" stroke="2" class="text-zinc-500" />
                  <span>{{ currentType?.minutes }} Minutes</span>
                </div>
              </div>
              <!-- Skselton Loader -->
              <div v-else class="space-y-2">
                <div class="h-5 w-1/2 bg-black/5 rounded animate-pulse"></div>
                <div class="flex items-center gap-x-2">
                  <div class="w-4 h-4 aspect-square shrink-0 bg-black/5 rounded-full animate-pulse"></div>
                  <span class="h-3 w-1/3 bg-black/5 rounded animate-pulse"></span>
                </div>
              </div>
            </div>
          </div>

          <!-- Date List [Column] -->
          <div class="p-5 overflow-y-auto">
            <div v-if="dateList.length">
              <ul class="space-y-3">
                <li v-for="(date, index) in dateList" :key="index">
                  <button :onclick="() => setCurentTimes(date)" class="w-full p-4 block bg-white border rounded-md transition cursor-pointer"
                    :class="selectedDate.date == date.date ? 'bg-[#F5F5F5] font-semibold border-sky-400' : 'bg-white border-[#D6D7D9]'">
                    <h3>{{ date.display }}</h3>
                  </button>
                </li>
              </ul>
            </div>

            <!-- Skeleton Loader -->
            <div v-else class="space-y-3">
              <div class="w-full h-14 bg-black/5 rounded animate-pulse"></div>
              <div class="w-full h-14 bg-black/5 rounded animate-pulse"></div>
              <div class="w-full h-14 bg-black/5 rounded animate-pulse"></div>
            </div>
          </div>

          <!-- Time List [Column] -->
          <div class="p-5 overflow-y-auto">
            <div v-if="timeList.length">
              <h4 class="mb-3 text-lg font-bold">{{ selectedDate.display }}</h4>
              <ul class="space-y-3">
                <li v-for="(time, index) in timeList" :key="index">
                  <button :onclick="() => setSelectedTime(time)" class="w-full p-2 block bg-white border border-[#D6D7D9] rounded-md cursor-pointer"
                    :class="selectedTime.time == time.time ? 'bg-[#F5F5F5] font-semibold border-sky-400' : 'bg-white border-[#D6D7D9]'">
                    <h3>{{ time.display }}</h3>
                  </button>
                </li>
              </ul>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
