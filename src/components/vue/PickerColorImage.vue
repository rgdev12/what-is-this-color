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
        <img @click="test" src="" id="show-image">
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

function test() {
  console.log('test');
}

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

function closeToast() {
  showToast.value = false;
}

</script>