<template>
  <div class="btn-row">
    <input type="file" accept="image/*" ref="fileInput" style="display:none" @change="onImageChange" />
    <button @click="openImage">Load Image</button>
    <button @click="toggleCamera">{{ streaming === 'camera' ? 'Close' : 'Open' }} Webcam</button>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import { Webcam } from '../utils/webcam.js';

const props = defineProps({
  imageRef: Object,
  cameraRef: Object,
  videoRef: Object
});
const streaming = ref(null);
const fileInput = ref(null);
const webcam = new Webcam();

function openImage() {
  fileInput.value.click();
}

function onImageChange(e) {
  const file = e.target.files[0];
  if (!file) return;
  const url = URL.createObjectURL(file);
  props.imageRef.src = url;
  streaming.value = 'image';
}

function toggleCamera() {
  if (streaming.value === 'camera') {
    webcam.close(props.cameraRef);
    streaming.value = null;
  } else {
    webcam.open(props.cameraRef);
    streaming.value = 'camera';
  }
}
</script>

<style>
.btn-row {
  margin-top: 10px;
  display: flex;
  gap: 10px;
}
</style>