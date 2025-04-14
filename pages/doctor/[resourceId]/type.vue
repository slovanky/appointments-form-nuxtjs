<script setup>
import axios from 'axios'
import { onMounted, ref } from 'vue'
import { useRoute } from 'vue-router'

const route = useRoute()
const resourceId = route.params.resourceId

const typeList = ref([])
const matchedTypes = ref(null)

onMounted(async () => {
  try {
    const response = await axios.post(
      'https://staging.ehjzny.com/api/v1/availability/types',
      {},
      {
        headers: {
          'X-APP-ID': 'h66w3DSEcsKlu8ReaclX'
        }
      }
    )

    typeList.value = response.data?.result || []

    matchedTypes.value = getTypeByResourceId(resourceId)

  } catch (error) {
    console.error('API Error:', error)
  }
})

function getTypeByResourceId(resourceId) {
  return typeList.value.find(type =>
    type.resources?.some(resource => resource.id === resourceId)
  )
}
</script>

<template>
  <section class="grow">
    <div class="wrapper">
      <div class="">
        <h2 class="text-lg text-center font-semibold mb-4">Available Types</h2>

        <div v-if="typeList.length">
          <ul class="space-y-5">
            <li v-for="(type, index) in typeList" :key="index">
              <NuxtLink :to="'/doctor/' + resourceId + '/' + type.id" class="p-4 block space-y-3 bg-white border border-[#D6D7D9] rounded-md shadow">
                <div class="flex items-center justify-between gap-x-4 gap-y-2">
                  <h3 class="text-lg font-bold capitalize">{{ type.name }}</h3>
                  <span class="px-3 py-0.5 bg-zinc-100 text-sm font-bold border border-zinc-200 rounded-full">{{ type.price == 0 ? 'FREE' : type.price }}</span>
                </div>
                <p class="text-zinc-500">
                  {{ type.description }}
                </p>
                <div class="flex items-end gap-x-1.5">
                  <span class="font-bold">{{ type.minutes }}</span>
                  <span class="text-sm text-zinc-400">Minutes session</span>
                </div>
              </NuxtLink>
            </li>
          </ul>
        </div>

        <div v-else class="text-gray-500 mt-4">Loading...</div>
      </div>
    </div>
  </section>
</template>
