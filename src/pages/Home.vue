<script setup lang="ts">
import TopNavbar from '../components/TopNavbar.vue'
import BoxItems from '../components/BoxItems.vue'


import { ref, onMounted, computed } from 'vue'
import axios from 'axios'

interface TemperatureData {
  datetime: string
  temperature: number
}

// Interface for the raw Open-Meteo response structure
interface OpenMeteoResponse {
  latitude: number
  longitude: number
  hourly: {
    time: string[]
    temperature_2m: number[]
  }
}

const temperatureDatas = ref<TemperatureData[]>([])

onMounted(async () => {
  try {
    const response = await axios.get<OpenMeteoResponse>(
      'https://api.open-meteo.com/v1/forecast?latitude=-6.2&longitude=106.8&hourly=temperature_2m'
    )

    temperatureDatas.value = response.data.hourly.time.map((timeStr, index) => ({
      datetime: timeStr,
      temperature: response.data.hourly.temperature_2m[index]
    }))

    console.log(temperatureDatas.value)
  } catch (error) {
    console.error('Failed to fetch temperature data:', error)
  }
})


const currentPage = ref(0)
const itemsPerPage = 12

const totalPages = computed(() => {
  return Math.ceil(temperatureDatas.value.length / itemsPerPage)
})

const paginatedData = computed(() => {
  const start = currentPage.value * itemsPerPage
  const end = start + itemsPerPage
  return temperatureDatas.value.slice(start, end)
})

const nextPage = () => {
  if (currentPage.value < totalPages.value - 1) {
    currentPage.value++
  }
}

const prevPage = () => {
  if (currentPage.value > 0) {
    currentPage.value--
  }
}



</script>

<template>
    <div class="bg-gray-200 flex flex-col justify-center items-center h-screen">
        <TopNavbar></TopNavbar>

        <div class="flex gap-12 w-full justify-center items-center">
            <button
                  @click="prevPage"
                  v-if="currentPage > 0"
                  class="bg-white rounded-xl p-8 text-center text-2xl font-extrabold hover:bg-gray-100 shadow"
            >
                  &lt;
            </button>

            <div v-else class="w-20"></div>

            <div class="flex flex-col items-center justify-center gap-8 w-4/5">
                <div class="w-full grid grid-cols-6 grid-rows-2 gap-8">
                    <BoxItems
                        v-for="item in paginatedData"
                        :key="item.datetime"
                        :item="item"
                    ></BoxItems>
                </div>

                <p class="bg-white p-4 font-bold">{{currentPage + 1}} / {{totalPages}}</p>
            </div>

            <button
                  @click="nextPage"
                  v-if="currentPage < totalPages - 1"
                  class="bg-white rounded-xl p-8 text-center text-2xl font-extrabold hover:bg-gray-100 shadow"
            >
                  &gt;
                </button>
                <!-- Spacer if button is hidden -->
            <div v-else class="w-20"></div>
        </div>

    </div>

</template>
