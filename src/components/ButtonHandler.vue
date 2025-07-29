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
  videoRef: Object,
  onImageLoad: Function
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
  
  // Afficher l'image et masquer la vidéo
  props.imageRef.style.display = 'block';
  props.cameraRef.style.display = 'none';
  
  streaming.value = 'image';
  
  // Déclencher la détection une fois l'image chargée
  props.imageRef.onload = () => {
    if (props.onImageLoad) {
      props.onImageLoad();
    }
  };
}

function toggleCamera() {
  if (streaming.value === 'camera') {
    webcam.close(props.cameraRef);
    
    // Masquer la vidéo
    props.cameraRef.style.display = 'none';
    
    streaming.value = null;
  } else {
    webcam.open(props.cameraRef);
    
    // Afficher la vidéo et masquer l'image
    props.cameraRef.style.display = 'block';
    props.imageRef.style.display = 'none';
    
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