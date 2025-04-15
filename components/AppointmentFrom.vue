<script setup>
import { onMounted, ref } from 'vue'
import axios from 'axios'

const fieldList = ref([])

onMounted(async () => {
  try {
    const response = await axios.get(
      'https://staging.ehjzny.com/api/v1/customFields/customer',
      {
        headers: {
          'X-APP-ID': 'h66w3DSEcsKlu8ReaclX',
          'lang': 'en'
        }
      }
    )

    fieldList.value = response.data?.result || []

    console.log(response.data?.result);

  } catch (error) {
    console.error('API Error:', error)
  }
})
</script>

<template>
  <div class="">
    <div v-if="fieldList.length" class="space-y-3">
      <div v-for="(field, index) in fieldList" :key="index" class="">
        <label class="flex flex-col gap-y-2">
          <span>{{ field.name }}</span>
          <input :type="field.type" :id="field.id" class="px-3 py-1.5 border border-zinc-300 focus:border-zinc-600 rounded outline-0">
        </label>
      </div>

      <button class="w-full mt-4 px-3 py-2 bg-zinc-900 hover:bg-zinc-700 text-white text-center font-bold rounded cursor-pointer">Confirm</button>
    </div>

    <div v-else class="">
      <!-- skeleton loader -->
      <div class="space-y-6">
        <div class="space-y-3">
          <div class="w-1/4 h-5 bg-zinc-200 rounded animate-pulse"></div>
          <div class="w-full h-8 bg-zinc-200 rounded animate-pulse"></div>
        </div>
        <div class="space-y-3">
          <div class="w-2/4 h-5 bg-zinc-200 rounded animate-pulse"></div>
          <div class="w-full h-8 bg-zinc-200 rounded animate-pulse"></div>
        </div>
        <div class="space-y-3">
          <div class="w-1/4 h-5 bg-zinc-200 rounded animate-pulse"></div>
          <div class="w-full h-8 bg-zinc-200 rounded animate-pulse"></div>
        </div>
      </div>
    </div>
  </div>
</template>
