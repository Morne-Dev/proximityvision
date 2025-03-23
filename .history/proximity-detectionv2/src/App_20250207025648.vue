<template>
  <div class="container min-h-content py-5 text-center">
    <div class="row py-lg-5">
      <div class="col-lg-6 col-md-8 mx-auto">
        <h1>Security Camera</h1>
        <p class="h4">Multiple object detection with emergency alarm</p>
        <div v-if="!showBtn" class="my-5">
          <div class="spinner-border spinner-border-sm" role="status"></div>
          <span> Loading...</span>
        </div>
        <section ref="demosSection" v-if="showBtn">
          <div ref="liveView" class="camView">
            <button v-if="!camStarted" class="btn btn-secondary" @click="enableCam">Enable Webcam</button>
            <div style="position: relative;">
              <video ref="video" autoplay muted style="width: 100%;"></video>
              <canvas ref="canvas" style="position: absolute; top: 0; left: 0;"></canvas>
            </div>
          </div>
          <p>The detected objects</p>
          <p ref="onTheScreen" style="height: 250px;"></p>
          <audio ref="audio">
            <source :src="alarm" type="audio/mp3">
            Your browser does not support the audio element.
          </audio>
        </section>
      </div>
    </div>
  </div>
</template>


<script setup>
import { ref, onMounted } from 'vue';
import '@tensorflow/tfjs-backend-cpu';
import '@tensorflow/tfjs-backend-webgl';
import * as cocoSsd from '@tensorflow-models/coco-ssd';

const video = ref(null);
const canvas = ref(null);
const liveView = ref(null);
const demosSection = ref(null);
const onTheScreen = ref(null);
const audio = ref(null);
const showBtn = ref(false);
const camStarted = ref(false);
let model;

onMounted(async () => {
  model = await cocoSsd.load();
  showBtn.value = true;
});

async function enableCam() {
  if (!model) return;
  camStarted.value = true;
  const constraints = { video: true };
  const stream = await navigator.mediaDevices.getUserMedia(constraints);
  video.value.srcObject = stream;
  video.value.addEventListener('loadeddata', predictWebcam);
}

function predictWebcam() {
  const ctx = canvas.value.getContext('2d');
  canvas.value.width = video.value.videoWidth;
  canvas.value.height = video.value.videoHeight;

  async function detectFrame() {
    const predictions = await model.detect(video.value);
    ctx.clearRect(0, 0, ctx.canvas.width, ctx.canvas.height);

    predictions.forEach(prediction => {
      if (prediction.score > 0.66) {
        const [x, y, width, height] = prediction.bbox;
        ctx.strokeStyle = 'yellow';
        ctx.lineWidth = 2;
        ctx.strokeRect(x, y, width, height);
        ctx.fillStyle = 'yellow';
        ctx.font = '18px Arial';
        ctx.fillText(
          `${prediction.class} (${(prediction.score * 100).toFixed(1)}%)`,
          x,
          y > 10 ? y - 5 : 10
        );

        if (prediction.class === 'person') {
          audio.value.play();
        }
      }
    });

    requestAnimationFrame(detectFrame);
  }

  detectFrame();
}
</script>


<style scoped>
body {
    font-family: helvetica, arial, sans-serif;
    margin: 2em;
    color: #3D3D3D;
  }

  video {
    display: block;
  }
  
  section {
    opacity: 1;
    transition: opacity 500ms ease-in-out;
  }

  .camView {
    position: relative;
    float: left;
    width: calc(100% - 20px);
    margin: 10px;
    cursor: pointer;
  }
  
  .camView p {
    position: absolute;
    padding: 5px;
    background-color: rgba(255, 111, 0, 0.85);
    color: #FFF;
    border: 1px dashed rgba(255, 255, 255, 0.7);
    z-index: 2;
    font-size: 12px;
  }
  
</style>