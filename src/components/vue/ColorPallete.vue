<template>
  <div class="bg-white rounded-md py-10 px-2 sm:px-0 flex justify-center items-center">
    <section class="max-w-96 sm:max-w-[570px] w-full border-2 p-3 rounded-md">
      <div class="flex flex-wrap items-end justify-between">
        <div>
          <label for="txtInputColor" class="text-slate-600">Ingresar color en HEX</label>
          <input @input="updateInputColor" v-model="txtInputColor" type="text" id="txtInputColor" class="w-full outline-none border border-slate-300 rounded mt-1 py-1 px-3">
        </div>

        <div class="flex items-center space-x-1 mb-1 sm:mt-0 mt-2">
          <input v-model="colorInput" type="color" class="cursor-pointer" @change="updateInputText">
          <img class="w-5" src="src/assets/icons/color-picker.svg" alt="Sun Icon" loading="lazy"/>
        </div>

        <div class="flex sm:justify-start justify-end w-full mt-3">
          <button @click="generatePalette" class="text-slate-600 hover:bg-[#F8FAFC] px-3 py-1 rounded-md border border-slate-500">Generar paleta</button>
        </div>
      </div>

      <div class="mt-6">
        <div v-for="(color, index) in colorPalette" :key="index" :style="{ backgroundColor: color }"
          class="color-container h-8 hover:h-28 transform transition-all flex items-end"
          :class="{
            'rounded-t' : index === 0,
            'rounded-b' : index === colorPalette.length - 1
          }">
          <span class="text-white hidden w-max p-1" style="background-color: rgba(57, 57, 57, 0.1)">{{ color }}</span>
        </div>
      </div>
    </section>
  </div>
</template>

<script lang="ts" setup>
import { ref, onMounted } from 'vue';

let txtInputColor = ref('#61A9D2');
let colorInput = ref('#61A9D2');
let colorPalette = ref<string[]>([]);

onMounted(() => {
  generatePalette();
});

const updateInputText = (event: Event) => {
  const target = event.target as HTMLInputElement;
  txtInputColor.value = target.value;
}

const updateInputColor = () => {
  // Si el color ingresado es válido, actualiza el color de colorInput
  if (/^#[0-9A-F]{6}$/i.test(txtInputColor.value)) {
    colorInput.value = txtInputColor.value;
  }
}

function generatePalette() {
  // Convierte el color ingresado a un formato adecuado (por ejemplo, #RRGGBB)
  const formattedColor = txtInputColor.value.startsWith('#')
    ? txtInputColor.value
    : `#${txtInputColor.value}`;

  // Calcula colores análogos con variabilidad
  const analogousColors = calculateAnalogousColors(formattedColor);

  // Almacena los colores en this.colorPalette
  colorPalette.value = analogousColors;
}

function calculateAnalogousColors(baseColor: string) {
  const numAnalogousColors = 4;
  const baseRgb = hexToRgb(baseColor);

  // Calcula el ángulo entre los colores análogos
  const angle = 360 / 12;

  const analogousColors = [];
  analogousColors.push(baseColor);
  // Genera los colores análogos con variabilidad
  for (let i = 0; i < numAnalogousColors; i++) {
    // Calcula un factor aleatorio para ajustar el ángulo
    const randomFactor = Math.random() * 0.2 + 0.9;

    // Calcula el nuevo ángulo con variabilidad
    const newAngle = angle * i * randomFactor;
    
    // Calcula los componentes RGB para el color análogo
    const r = Math.round(baseRgb.r + Math.cos(newAngle) * 50);
    const g = Math.round(baseRgb.g + Math.sin(newAngle) * 50);
    const b = Math.round(baseRgb.b);

    // Convierte los componentes RGB al formato hexadecimal
    const analogousHex = rgbToHex(r, g, b);
    analogousColors.push(analogousHex);
  }

  return analogousColors;
}

function hexToRgb(hex: string) {
  const bigint = parseInt(hex.slice(1), 16);
  const r = (bigint >> 16) & 255;
  const g = (bigint >> 8) & 255;
  const b = bigint & 255;

  return { r, g, b };
}

function rgbToHex(r: number, g: number, b: number) {
  return `#${(1 << 24 | r << 16 | g << 8 | b).toString(16).slice(1)}`;
}
</script>

<style>
/* El span dentro de color-container aparecerá si se hace hover en color-container */
.color-container:hover span {
  display: block;
}
</style>