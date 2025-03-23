<template>
  <div class="container min-h-content py-5 text-center">
    <div class="row py-lg-5">
      <div class="col-lg-6 col-md-8 mx-auto">
        <h2>Proximity Model Test</h2>
        <p class="h4"></p>

      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import '@tensorflow/tfjs-backend-cpu'
import '@tensorflow/tfjs-backend-webgl'
import * as cocoSsd from '@tensorflow-models/coco-ssd'
import alarm from './assets/alarm.mp3'

const video = reff(null)
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
})



</script>