<template>
  <div class="max-w-xs bg-red-100 border border-red-200 text-sm text-red-800 rounded-lg dark:bg-red-800/10 dark:border-red-900 dark:text-red-500 absolute top-0 end-0 m-5" role="alert">
    <div class="flex p-4">
      {{ message }}

      <div class="ms-auto">
        <button @click="closeToast" type="button" class="inline-flex flex-shrink-0 justify-center items-center size-5 rounded-lg text-red-800 opacity-50 hover:opacity-100 focus:outline-none focus:opacity-100 dark:text-red-200">
          <span class="sr-only">Close</span>
          <svg class="flex-shrink-0 size-4" xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
            <path d="M18 6 6 18"></path>
            <path d="m6 6 12 12"></path>
          </svg>
        </button>
      </div>
    </div>
  </div>
</template>

<script lang="ts" setup>
import { ref, defineProps, onMounted } from 'vue';

const emit = defineEmits(['close'])

// establecemos las props: message, type, duration. que son los datos que recibirá el componente
const props = defineProps({
  message: {
    type: String,
    required: true
  },
  type: {
    type: String,
    default: 'info'
  },
  duration: {
    type: Number,
    default: 5000
  }
});

onMounted(() => {
  setTimeout(() => {
    emit('close');
  }, props.duration);
});

function closeToast() {
  emit('close');
}

</script>