<template>
  <div class="bg-white rounded-md py-10 px-2 sm:px-0 flex justify-center items-center">
    <Toast v-if="showToast" :message="toastMessage" @close="closeToast"/>
    <div class="max-w-96 sm:max-w-[550px] w-full border-2 p-3 rounded-md">
      <div class="relative w-full h-32 rounded-sm flex justify-center items-center flex-col border-2 border-dashed cursor-pointer" @click="openFile">
        <input @change="loadImage" type="file" id="upload-image" accept="image/*" hidden>
        <img class="" src="src/assets/icons/Upload.svg" alt="Upload Icon" />
        <h3>Cargar imagen</h3>
        <p class="text-sm sm:text-base">arrastra y suelta o selecciona una imagen</p>
      </div>

      <div class="mt-5 w-full">
        <img src="" alt="" id="show-image">
      </div>
    </div>
  </div>
</template>

<script lang="ts" setup>
import { ref } from 'vue';
import Toast from './Toast.vue';

let toastMessage = ref('');
let showToast = ref(false);

function openFile() {
  const input = document.getElementById('upload-image');
  input?.click();
}

function loadImage() {
  const input = document.getElementById('upload-image') as HTMLInputElement;
  const image = document.getElementById('show-image') as HTMLImageElement;

  if (input.files && input.files[0]) {
    // validamos que sea una imagen
    if (!input.files[0].type.includes('image')) {
      toastMessage.value = 'El archivo seleccionado no es una imagen';
      showToast.value = true;
      return;
    }

    const reader = new FileReader();
    reader.onload = (e) => {
      image.src = e.target?.result as string;
    };
    reader.readAsDataURL(input.files[0]);
  }
}

function closeToast() {
  showToast.value = false;
}

</script>