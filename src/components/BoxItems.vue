<script setup lang="ts">
  import { computed } from 'vue'
  interface TemperatureData {
    datetime: string
    temperature: number
  }

  const props = defineProps<{
    item: TemperatureData
  }>()

  const temperatureImage = computed(() => {
    const temp = props.item.temperature

    if (temp < 20) {
      return new URL('../assets/cold.jpg', import.meta.url).href
    } else if (temp <= 30) {
      return new URL('../assets/normal.jpg', import.meta.url).href
    } else {
      return new URL('../assets/hot.jpg', import.meta.url).href
    }
  })

</script>

<template>
    <div class="bg-white p-8 flex w-full rounded-xl">
        <div class="flex flex-col">
            <div class="flex flex-col w-full gap-2">
                <h2 class="text-l font-semibold">{{ item.datetime.split('T')[0] }}</h2>
                <h2 class="text-l font-semibold">{{ item.datetime.split('T')[1] }}</h2>
            </div>
            <div class="flex w-full mt-4 gap-2 justify-center items-center">
                <img :src="temperatureImage" alt="" class="w-10 h-24 object-cover" />
                <h1 class="text-3xl font-bold text-center w-full block">{{ item.temperature }} °C</h1>
            </div>
        </div>
    </div>
</template>
