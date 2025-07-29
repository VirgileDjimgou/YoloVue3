<template>
  <div class="app">
    <Loader v-if="loading">Loading model... {{ progress }}%</Loader>
    <video ref="video" class="overlay" autoplay muted playsinline @play="onVideoPlay" />
    <canvas ref="canvas" class="overlay" />
    <img ref="image" class="overlay" />
    <ButtonHandler :imageRef="image" :cameraRef="video" />
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import * as tf from '@tensorflow/tfjs';
import '@tensorflow/tfjs-backend-webgl';
import { detectVideo, detect } from './utils/detect.js';
import Loader from './components/Loader.vue';
import ButtonHandler from './components/ButtonHandler.vue';

const video = ref(null);
const canvas = ref(null);
const image = ref(null);

const loading = ref(true);
const progress = ref(0);

let model, inputShape;

async function loadModel() {
  const handler = tf.io.browserHTTPRequest('/model/model.json', {
    onProgress: (f) => { progress.value = Math.round(f * 100); }
  });
  const net = await tf.loadGraphModel(handler);
  model = net;
  inputShape = net.inputs[0].shape;
  // Ajouter inputShape comme propriété du modèle pour compatibilité
  model.inputShape = inputShape;
  loading.value = false;
}

async function onVideoPlay() {
  canvas.value.width = video.value.videoWidth;
  canvas.value.height = video.value.videoHeight;
  detectVideo(video.value, model, canvas.value);
}

onMounted(async () => {
  await loadModel();
});
</script>

<style src="./style/App.css"></style>