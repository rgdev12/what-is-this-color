<template>
  <div class="bg-white rounded-md py-10 px-2 sm:px-0 flex justify-center items-center">
    <Toast v-if="showToast" :message="toastMessage" @close="closeToast"/>
    <div class="max-w-96 sm:max-w-[570px] w-full border-2 p-3 rounded-md">
      <div class="rounded-sm border-2 border-dashed cursor-pointer"
           @dragover.prevent="onDragOver" @dragleave.prevent="onDragLeave" @drop.prevent="drop" @click="openFile">
        <div v-if="!isDragging" class="relative w-full h-32 flex justify-center items-center flex-col">
          <input @change="loadImage" type="file" id="upload-image" accept="image/*" hidden>
          <img class="" src="src/assets/icons/Upload.svg" alt="Upload Icon" />
          <h3>Cargar imagen</h3>
          <p class="text-sm sm:text-base">arrastra y suelta o selecciona una imagen</p>
        </div>

        <div v-else class="relative w-full h-32 flex justify-center items-center flex-col">
          <p>Soltar aquí</p>
        </div>
      </div>

      <div class="mt-5 w-full">
        <img @click="getColor" src="" id="show-image" class="cursor-crosshair">
      </div>
    </div>
  </div>
</template>

<script lang="ts" setup>
import { ref } from 'vue';
import Toast from './Toast.vue';

declare global {
  class EyeDropper {
    open(): Promise<{ sRGBHex: string }>;
    close(): void;
  }
}

let toastMessage = ref('');
let showToast = ref(false);

let isDragging = ref(false);

function openFile() {
  const input = document.getElementById('upload-image');
  input?.click();
}

function loadImage() {
  const input = document.getElementById('upload-image') as HTMLInputElement;

  if (input.files && input.files[0]) {
    // validamos que sea una imagen
    if (!input.files[0].type.includes('image')) {
      toastMessage.value = 'El archivo seleccionado no es una imagen';
      showToast.value = true;

      // limpiamos el input
      input.value = '';
      return;
    }

    showImage(input.files[0]);
  }
}

function onDragOver(event: any) {
  event.preventDefault();
  isDragging.value = true;
}

function onDragLeave(event: any) {
  event.preventDefault();
  isDragging.value = false;
}

function drop(event: any) {
  event.preventDefault();
  isDragging.value = false;
  const file = event.dataTransfer.files[0];

  // validamos que sea una imagen
  if (!file.type.includes('image')) {
    toastMessage.value = 'El archivo seleccionado no es una imagen';
    showToast.value = true;
    return;
  }

  showImage(file);

  event.dataTransfer.clearData();
}

function showImage(file: File) {
  const image = document.getElementById('show-image') as HTMLImageElement;
  const reader = new FileReader();
  reader.onload = (e) => {
    image.src = e.target?.result as string;
  };
  reader.readAsDataURL(file);
}

function getColor(event: any) {
  const canvas = document.createElement("canvas");
  const ctx = canvas.getContext("2d") as CanvasRenderingContext2D;
  const img = document.getElementById("show-image") as HTMLImageElement;

  canvas.width = img.width;
  canvas.height = img.height;
  ctx.drawImage(img, 0, 0, img.width, img.height);

  const x = event.offsetX; // Coordenada X relativa al canvas
  const y = event.offsetY; // Coordenada Y relativa al canvas
  const pixel = ctx.getImageData(x, y, 1, 1).data;

  // convertimos el color a hexadecimal
  const hexColor = rgbToHex(pixel[0], pixel[1], pixel[2]);
  console.log(`Color HEX en (${x}, ${y}): ${hexColor}`);

  const color = `rgb(${pixel[0]}, ${pixel[1]}, ${pixel[2]})`;
  console.log(`Color RGB en (${x}, ${y}): ${color}`);
}

function rgbToHex(r: number, g: number, b: number): string {
  const validR = Math.min(255, Math.max(0, r));
  const validG = Math.min(255, Math.max(0, g));
  const validB = Math.min(255, Math.max(0, b));

  // Convierte cada componente a su valor hexadecimal
  const hexR = validR.toString(16).padStart(2, "0");
  const hexG = validG.toString(16).padStart(2, "0");
  const hexB = validB.toString(16).padStart(2, "0");

  // Combina los componentes hexadecimales para formar el código completo
  const hexColor = `#${hexR}${hexG}${hexB}`;
  return hexColor.toUpperCase();
}

function closeToast() {
  showToast.value = false;
}

</script>