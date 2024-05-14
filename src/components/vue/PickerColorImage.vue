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

      <!-- <div class="mt-5 w-full">
        <img @click="getColor" src="" id="show-image" class="cursor-crosshair">
      </div> -->

      <div class="mt-5 w-full">
        <section class="color-img cursor-crosshair relative">
          <img ref="uploadImage" src="src/assets/img/test-image.jpg" alt="upload" class="w-full" id="show-image" @mousemove="moveLens" @mouseover="removeHidden">
          <div ref="lens" id="magnifier-lens-img" class="magnifier-lens absolute w-[150px] h-[100px] border border-black hidden"
            @click="getColor" @mousemove="moveLens" @mouseout="removeStyle"></div>
        </section>
      </div>
    </div>
  </div>
</template>

<script lang="ts" setup>
import { ref } from 'vue';
import Toast from './Toast.vue';

let toastMessage = ref('');
let showToast = ref(false);
let isDragging = ref(false);

const uploadImage = ref(document.createElement('img'));
const lens = ref(document.createElement('div'));

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
  if (file) {
    const imageUrl = URL.createObjectURL(file);
    image.src = imageUrl;
  }
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

const moveLens = (event: any) => {
  let x, y, cx, cy;

  const uploadImageReact = uploadImage.value.getBoundingClientRect();
  x = event.x - uploadImageReact.left - lens.value.offsetWidth / 2;
  y = event.y - uploadImageReact.top - lens.value.offsetHeight / 2;

  let max_xpos = (uploadImageReact.width - lens.value.offsetWidth) + 77;
  let max_ypos = (uploadImageReact.height - lens.value.offsetHeight) + 65;
  // console.log(max_xpos, max_ypos, x, y);

  if (x > max_xpos) x = max_xpos;
  if (x < -56) x = -56;

  if (y > max_ypos) y = max_ypos;
  if (y < -45) y = -45;

  lens.value.style.cssText = `top: ${y}px; left: ${x}px;`;

  // calculamos la imagen magnificada para mostrarla en el lens
  cx = (lens.value.offsetWidth) / uploadImage.value.offsetWidth * 10;
  cy = (lens.value.offsetHeight) / uploadImage.value.offsetHeight * 10;

  let xcx = x * cx + 100;
  xcx = xcx < 0 ? xcx * (-1) : -xcx;

  let ycy = y * cy + 100;
  ycy = ycy < 0 ? ycy * (-1) : -ycy;

  lens.value.style.backgroundImage = `url('${uploadImage.value.src}')`;
  lens.value.style.backgroundSize = `${uploadImage.value.offsetWidth * cx - 30}px ${uploadImage.value.offsetHeight * cy + 20}px`;
  lens.value.style.backgroundPosition = `${xcx}px ${ycy}px`;
  lens.value.style.backgroundRepeat = 'no-repeat';

  // dibujamos un punto en el centro del lens
  lens.value.innerHTML = '<div class="magnifier-dot"></div>';
}

const removeStyle = () => {
  lens.value.style.display = 'none';
}

const removeHidden = () => {
  lens.value.classList.remove('hidden');
}

function closeToast() {
  showToast.value = false;
}

</script>


<style>
.magnifier-dot {
  position: absolute;
  width: 5px;
  height: 5px;
  background-color: red;
  border-radius: 50%;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}
</style>