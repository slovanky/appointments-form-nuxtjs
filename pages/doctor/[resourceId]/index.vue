<script setup>
import { IconUser } from '@tabler/icons-vue'
import axios from 'axios'
import { onMounted, ref } from 'vue'
import { useRoute } from 'vue-router'

const route = useRoute()
const resourceId = route.params.resourceId

const typeList = ref([])
const matchedTypes = ref(null)
const currentResource = ref({})

onMounted(() => {
  fetchTypeList()
  fetchResource()
})

// Fetching Type List
async function fetchTypeList() {
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

    console.log('Resource: ', response.data?.result);

  } catch (error) {
    console.error('Fetching Resource Error:', error)
  }
}

function getTypeByResourceId(resourceId) {
  return typeList.value.find(type =>
    type.resources?.some(resource => resource.id === resourceId)
  )
}
</script>

<template>
  <section class="grow">
    <div class="wrapper">
      <div>
        <h2 class="text-lg text-center font-semibold mb-4">Available Types</h2>

        <div class="p-5 space-y-5 bg-white border-[#D6D7D9] rounded-lg shadow">
          <div>
            <div v-if="currentResource?.name" class="flex items-center gap-x-2">
              <div class="w-8 h-8 aspect-square shrink-0 flex items-center justify-center bg-zinc-100 rounded-full">
                <IconUser size="18" stroke="2" class="text-zinc-500" />
              </div>
              <h4 class="text-sm font-bold">{{ currentResource?.name }}</h4>
            </div>
            <!-- Skeleton Loader -->
            <div v-else class="flex items-center gap-x-2">
              <div class="w-8 h-8 aspect-square shrink-0 bg-black/5 rounded-full animate-pulse"></div>
              <div class="h-3 w-32 bg-black/5 rounded animate-pulse"></div>
            </div>
          </div>

          <div v-if="typeList.length">
            <ul class="space-y-5">
              <li v-for="(type, index) in typeList" :key="index">
                <NuxtLink :to="'/doctor/' + resourceId + '/' + type.id" class="p-4 block space-y-3 bg-white hover:bg-zinc-100 border border-[#D6D7D9] rounded-md">
                  <div class="flex items-center justify-between gap-x-4 gap-y-2">
                    <h3 class="text-lg font-bold capitalize">{{ type.name }}</h3>
                    <span class="px-3 py-0.5 bg-zinc-100 text-sm font-bold border border-zinc-200 rounded-full">{{ type.price == 0 ? 'FREE' : `$${type.price}` }}</span>
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

          <!-- Skeleton Loader -->
          <div v-else class="space-y-5">
            <div class="w-full h-24 px-4 py-5 space-y-4 bg-black/5 rounded">
              <div class="w-1/4 h-6 bg-black/10 rounded animate-pulse"></div>
              <div class="w-1/5 h-4 bg-black/10 rounded animate-pulse"></div>
            </div>
            <div class="w-full h-24 px-4 py-5 space-y-4 bg-black/5 rounded">
              <div class="w-1/4 h-6 bg-black/10 rounded animate-pulse"></div>
              <div class="w-1/5 h-4 bg-black/10 rounded animate-pulse"></div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </section>
</template>
