<template>
  <div class="bg-white rounded-md py-10 px-2 sm:px-0 flex justify-center items-center">
    <section class="max-w-96 sm:max-w-[570px] w-full border-2 p-3 rounded-md">
      <div class="cursor-pointer">
        <p class="text-center text-sm mb-2 text-slate-500">Haz clic para escoger un color</p>
        <input type="color" id="primary_color" value="#3298D0" class="w-full h-10 outline-none cursor-pointer" @input="getColor">
      </div>

      <div class="mt-5">
        <p>Conversión de colores: </p>

        <div class="w-full">
          <div class="flex justify-between items-center border border-slate-200 py-2 px-3 rounded-md mt-2 mb-4">
            <p>HEX:</p>
            <div class="flex items-center space-x-1 cursor-pointer" @click="copyColorToClipboard(hexColor)">
              <p>{{ hexColor }}</p>
              <img class="w-5" src="src/assets/icons/clipboard.svg" alt="clipboar icon" />
            </div>
          </div>
          <div class="flex justify-between items-center border border-slate-200 py-2 px-3 rounded-md my-4">
            <p>RGB:</p>
            <div class="flex items-center space-x-1 cursor-pointer" @click="copyColorToClipboard(rgbColor)">
              <p>{{ rgbColor }}</p>
              <img class="w-5" src="src/assets/icons/clipboard.svg" alt="clipboar icon" />
            </div>
          </div>
          <div class="flex justify-between items-center border border-slate-200 py-2 px-3 rounded-md my-4">
            <p>HSL:</p>
            <div class="flex items-center space-x-1 cursor-pointer" @click="copyColorToClipboard(hslColor)">
              <p>{{ hslColor }}</p>
              <img class="w-5" src="src/assets/icons/clipboard.svg" alt="clipboar icon" />
            </div>
          </div>
        </div>
      </div>
    </section>
  </div>
</template>

<script lang="ts" setup>
import { ref } from 'vue';
import { useToast } from "vue-toastification";

const toast = useToast();

let hexColor = ref('#3298D0');
let rgbColor = ref('rgb(50, 152, 208)');
let hslColor = ref('hsl(200, 70%, 50%)');

const getColor = (event: Event) => {
  const target = event.target as HTMLInputElement;
  const color = target.value;
  
  // obtenemos los 3 formatos de color
  hexColor.value = color.toUpperCase();
  rgbColor.value = hexToRgb(color);
  hslColor.value = hexToHsl(color);
}

function hexToRgb(hex: string) {
  const r = parseInt(hex.substring(1, 3), 16);
  const g = parseInt(hex.substring(3, 5), 16);
  const b = parseInt(hex.substring(5, 7), 16);
  return `rgb(${r}, ${g}, ${b})`;
}

function hexToHsl(hex: string) {
  const r = parseInt(hex.substring(1, 3), 16) / 255;
  const g = parseInt(hex.substring(3, 5), 16) / 255;
  const b = parseInt(hex.substring(5, 7), 16) / 255;

  const max = Math.max(r, g, b);
  const min = Math.min(r, g, b);
  let h = (max + min) / 2;
  let s = (max + min) / 2;
  let l = (max + min) / 2;

  if (max === min) {
    h = s = 0;
  } else {
    const d = max - min;
    s = l > 0.5 ? d / (2 - max - min) : d / (max + min);
    switch (max) {
      case r:
        h = (g - b) / d + (g < b ? 6 : 0);
        break;
      case g:
        h = (b - r) / d + 2;
        break;
      case b:
        h = (r - g) / d + 4;
        break;
    }
    h /= 6;
  }

  h = Math.round(h * 360);
  s = Math.round(s * 100);
  l = Math.round(l * 100);

  return `hsl(${h}, ${s}%, ${l}%)`;
}

function copyColorToClipboard(color: string) {
  navigator.clipboard.writeText(color);
  toast.info('Color copiado al portapapeles', {timeout: 2000});
}
</script>

<style>

#primary_color::-webkit-color-swatch-wrapper {
  padding: 0; 
}
#primary_color::-webkit-color-swatch {
  border: none;
}
</style>