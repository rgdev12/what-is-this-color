<template>
  <div class="bg-white rounded-md py-10 px-2 sm:px-0 flex justify-center items-center">
    <section class="max-w-96 sm:max-w-[570px] w-full border-2 p-3 rounded-md">
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
          <img ref="uploadImage" src="src/assets/img/example.jpeg" alt="upload" class="w-full" id="show-image"
            @mousemove="moveLens" @mouseover="removeHidden" @mouseout="removeStyle" @click="getColor">
          <div ref="lens" id="magnifier-lens-img" class="magnifier-lens absolute w-[150px] h-[100px] border border-black bg-white hidden"></div>
        </section>
      </div>

      <div class="mt-6">
        <div ref="showColor" class="color-show w-full h-8 rounded-sm bg-gray-200 cursor-pointer flex justify-center items-center" @click="copyColor">
          <img class="w-5 hover-change-colo-svg" src="src/assets/icons/clipboard.svg" alt="Sun Icon" />
        </div>
        <p class="text-sm text-center mt-1 text-slate-500">
          {{ !textColor ? 'Haz clic en la imagen para esoger un color' : textColor }}
        </p>
      </div>
    </section>
  </div>
</template>

<script lang="ts" setup>
import { ref } from 'vue';
import { useToast } from "vue-toastification";

let isDragging = ref(false);
let textColor = ref('');

const toast = useToast();
const uploadImage = ref(document.createElement('img'));
const lens = ref(document.createElement('div'));
const showColor = ref(document.createElement('div'));

function openFile() {
  const input = document.getElementById('upload-image');
  input?.click();
}

function loadImage() {
  const input = document.getElementById('upload-image') as HTMLInputElement;

  if (input.files && input.files[0]) {
    // validamos que sea una imagen
    if (!input.files[0].type.includes('image')) {
      toast.error('El archivo seleccionado no es una imagen', {timeout: 4000});

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
    toast.error('El archivo seleccionado no es una imagen', {timeout: 4000});
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

  const color = `rgb(${pixel[0]}, ${pixel[1]}, ${pixel[2]})`;

  showColor.value.style.backgroundColor = color;
  textColor.value = `${hexColor}`;
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
  let x, y;
  const uploadImageReact = uploadImage.value.getBoundingClientRect();

  // Calculate cursor position relative to image
  x = event.clientX - uploadImageReact.left;
  y = event.clientY - uploadImageReact.top;

  // Calculate lens position with centered offset
  const lensOffsetX = lens.value.offsetWidth / 2;
  const lensOffsetY = lens.value.offsetHeight / 2;

  lens.value.style.cssText = `
    top: ${y + 10}px;
    left: ${x + 10}px;
  `;

  // calculamos las dimensiones de la imagen ampliada
  const magnification = (lens.value.offsetWidth) / uploadImage.value.offsetWidth * 10;

  lens.value.style.backgroundImage = `url('${uploadImage.value.src}')`;
  lens.value.style.backgroundSize = `${uploadImage.value.offsetWidth * magnification}px ${uploadImage.value.offsetHeight * magnification}px`;

  // calculamos la posición del background para la vista ampliada
  const magnifiedX = x * magnification;
  const magnifiedY = y * magnification;

  lens.value.style.backgroundPosition = `${(magnifiedX - lensOffsetX) * (-1)}px ${(magnifiedY - lensOffsetY) * (-1)}px`;
  lens.value.style.backgroundRepeat = 'no-repeat';

  // dibujamos el punto en el centro del lente
  lens.value.innerHTML = '<div class="magnifier-dot"></div>';
};

const removeStyle = () => {
  lens.value.style.display = 'none';
}

const removeHidden = () => {
  lens.value.classList.remove('hidden');
}

const copyColor = () => {
  const color = textColor.value;
  navigator.clipboard.writeText(color);

  toast.info('Color copiado al portapapeles', {timeout: 4000});
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

/* Hacemos que la imagen dentro de color-show aparezca solo si se hace hover en el div y le damos una transición */
.color-show:hover img {
  opacity: 1;
  transition: opacity 0.3s ease-in-out;
}

/* Hacemos que la imagen dentro de color-show tenga una opacidad de 0 */
.color-show img {
  opacity: 0;
  transition: opacity 0.3s ease-in-out;
}
</style>