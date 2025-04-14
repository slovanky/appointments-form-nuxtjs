<script setup>
import axios from 'axios'
import { onMounted, ref } from 'vue'
import { useRoute } from 'vue-router'

const route = useRoute()
const resourceId = route.params.resourceId
const typeId = route.params.typeId

const dateList = ref([])

const selectedDate = ref("")
const timeList = ref([])

onMounted(async () => {
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
    console.error('API Error:', error)
  }
})

function setCurentTimes(date) {
  dateList.value.find(type => {
    if (type.date === date) {
      selectedDate.value = date
      timeList.value = type.times
    }
  })
}
</script>

<template>
  <div class="grow flex items-center justify-center">
    <div class="wrapper h-max">
      <div class="w-full">
        <h2 class="text-lg text-center font-semibold mb-4">Available Times</h2>

        <div class="h-[480px] grid grid-cols-3 divide-x divide-black/10 bg-white border border-[#D6D7D9] rounded-lg shadow overflow-hidden">
          <!-- <div class="p-3 overflow-y-auto"></div> -->
          <div class="p-3 overflow-y-auto">
            <div v-if="dateList.length">
              <ul class="space-y-3">
                <li v-for="(date, index) in dateList" :key="index">
                  <button :onclick="() => setCurentTimes(date.date)" class="w-full p-4 block bg-white border rounded-md transition cursor-pointer"
                    :class="selectedDate == date.date ? 'bg-[#F5F5F5] font-semibold border-sky-400' : 'bg-white border-[#D6D7D9]'">
                    <h3>{{ date.display }}</h3>
                  </button>
                </li>
              </ul>
            </div>

            <div v-else class="text-gray-500 mt-4">Loading...</div>
          </div>
          <div class="col-span-2 p-3 overflow-y-auto">
            <div v-if="timeList.length">
              <ul class="space-y-3">
                <li v-for="(time, index) in timeList" :key="index">
                  <button class="w-full p-2 block bg-white border border-[#D6D7D9] rounded-md cursor-pointer">
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
