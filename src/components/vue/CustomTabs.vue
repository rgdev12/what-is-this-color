<template>
  <div>
    <div class="bg-[#e5e5e9] py-3.5 px-1 rounded-md">
      <ul class="custom-tabs">
        <li class="inline mr-3 p-2 rounded-md" :class="{ 'bg-white': tab.active }" v-for="tab in tabs" :key="tab.index">
          <button @click="changeTab(tab.index)">{{ tab.title }}</button>
        </li>
      </ul>
    </div>

    <div class="mt-2" v-for="tab in tabs" :key="tab.index" v-show="tab.active">
      <slot :name="`tab-${tab.index}`"></slot>      
    </div>
  </div>
</template>

<script lang="ts" setup>
import { ref } from 'vue'

const tabs = ref([
  { index: 1, title: 'Seleccionar color de una imagen', active: true },
  { index: 2, title: 'Color Hexadecimal o RGB', active: false },
  { index: 3, title: 'Paleta de colores', active: false }
])

// función para cambiar de tab
const changeTab = (index: number) => {
  tabs.value.forEach(tab => {
    tab.active = tab.index === index
  })
}
</script>

<style>
.custom-tabs li {
  transition: background-color 0.3s ease; /* Adjust duration and timing as needed */
}
</style>