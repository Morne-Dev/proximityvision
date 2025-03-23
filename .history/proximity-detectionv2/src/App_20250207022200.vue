<template>
  <div class="container min-h-content py-5 text-center">
    <div class="row py-lg-5">
      <div class="col-lg-6 col-md-8 mx-auto">
        <h2>Proximity Model Test</h2>
        <p class="h4"></p>
        <div v-if="!showBtn" class="my-5">
          <div class="spinner-border spinner-border-sm" role="status"></div>
          <span class="" >Loading...</span>
        </div>

      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import '@tensorflow/tfjs-backend-cpu'
import '@tensorflow/tfjs-backend-webgl'
import * as cocoSsd from '@tensorflow-models/coco-ssd'


const video = ref(null)
const liveView = ref(null)
const demoSection = ref(null)
const onTheScreen = ref(null)
const audio = ref(null)
const showBtn = ref(false)
const camStarted = ref(false)

let model = undefined;

cocoSsd.load().then(function() {
  model = loadedModel;
  showBtn.value = true
});

async function enableCame(event) {
  if (!model) {
    return;
  }
  camStarted.value = true;
  const constraints = {
    video: true
  };

  //webcamera stream
  await navigator.mediaDevices.getUserMedia(constraints).then(function(stream) {
    try {
      video.value.srcObject = stream.value.srcObject
      console.log('try', stream)
    } catch (error) {
      console.log('catch', error)
    }
    video.value.addEventListener('loadeddata', predictWebcam);
  });
}



</script>