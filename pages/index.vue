<script setup>
import { IconUser } from '@tabler/icons-vue'
import axios from 'axios'
import { onMounted, ref } from 'vue'

const resourceList = ref([])

onMounted(async () => {
  try {
    const response = await axios.post(
      'https://staging.ehjzny.com/api/v1/availability/resources',
      {},
      {
        headers: {
          'X-APP-ID': 'h66w3DSEcsKlu8ReaclX'
        }
      }
    )

    resourceList.value = response.data?.result || []
    console.log('API Response:', response.data)
  } catch (error) {
    console.error('API Error:', error)
  }
})
</script>

<template>
  <section class="grow">
    <div class="wrapper">
      <div class="">
        <h2 class="text-lg text-center font-semibold mb-4">Available Doctors</h2>

        <div v-if="resourceList.length">
          <ul class="space-y-5">
            <li v-for="(resource, index) in resourceList" :key="index">
              <div class="p-4 block bg-white border border-[#D6D7D9] rounded-lg">
                <div class="flex items-start gap-x-4">
                  <div class="w-20 h-20 aspect-square shrink-0 flex items-center justify-center bg-zinc-100 rounded-full">
                    <IconUser size="36" stroke="2" class="text-zinc-500" />
                  </div>

                  <div class="grow pt-1 space-y-1">
                    <div class="w-full flex items-center justify-between gap-x-4 gap-y-2">
                      <h3 class="text-lg font-bold capitalize">{{ resource.name }}</h3>
                      <div class="px-3 py-0.5 flex items-center gap-x-2 bg-zinc-100 text-xs font-bold border border-zinc-200 rounded-full">
                        <span class="w-2 h-2 aspect-square shrink-0 rounded-full" :class="resource.on ? 'bg-lime-500' : 'bg-zinc-300'"></span>
                        <span>{{ resource.on ? 'ON' : 'OFF' }}</span>
                      </div>
                    </div>

                    <p class="text-zinc-500">
                      {{ resource.description }}
                    </p>

                    <NuxtLink :to="'/doctor/' + resource.id" class="px-3 py-1.5 inline-block bg-white hover:bg-zinc-100 text-sm font-bold border border-zinc-200 rounded">
                      Book Now
                    </NuxtLink>
                  </div>
                </div>
              </div>
            </li>
          </ul>
        </div>

        <!-- Skeleton Loader -->
        <div v-else class="space-y-5">
          <div class="w-full h-28 p-4 flex gap-x-3 bg-black/5 rounded">
            <div class="w-20 h-20 p-2 block shrink-0 bg-black/10 rounded-full animate-pulse"></div>
            <div class="grow pt-1 space-y-4">
              <div class="w-1/4 h-6 bg-black/10 rounded animate-pulse"></div>
              <div class=" w-24 h-8 bg-black/10 rounded animate-pulse"></div>
            </div>
          </div>

          <div class="w-full h-28 p-4 flex gap-x-3 bg-black/5 rounded">
            <div class="w-20 h-20 p-2 block shrink-0 bg-black/10 rounded-full animate-pulse"></div>
            <div class="grow pt-1 space-y-4">
              <div class="w-1/4 h-6 bg-black/10 rounded animate-pulse"></div>
              <div class=" w-24 h-8 bg-black/10 rounded animate-pulse"></div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </section>
</template>
