<template>
  <div class="container min-h-content py-5 text-center">
      <div class="row py-lg-5">
          <div class="col-lg-6 col-md-8 mx-auto">
              <h1 >Security Camera</h1> 
               <p class=" h4">Multiple object detection with emergency alarm</p>
                   <div v-if="!showBtn" class="my-5" >
                   <div  class="spinner-border spinner-border-sm" role="status"></div> 
                    <span class=""> Loading...</span>
                   </div>
              <section ref="demosSection" v-if="showBtn">

              <p> </p>
              <div ref="liveView" class="camView">
                  <button v-if="!camStarted" class="btn btn-secondary" @click="enableCam">Enable Webcam</button>
                  <video  ref="video"  autoplay muted style="height: 460; width: 100%;"></video>
              </div>
              <p >The detected objects</p>
              <p ref="onTheScreen" style="height: 250px;"></p>
              <audio ref="audio" >
                <source  :src="alarm" type="audio/mp3">
                Your browser does not support the audio element.
              </audio>
              </section>

          </div>
      </div>
  </div>
</template>

<script setup>
import {ref} from 'vue'
import '@tensorflow/tfjs-backend-cpu'
import '@tensorflow/tfjs-backend-webgl'
import * as cocoSsd from '@tensorflow-models/coco-ssd'


const video = ref(null)
const liveView = ref(null)
const demosSection = ref(null)
const onTheScreen = ref(null)
const audio = ref(null)
const showBtn = ref(false)
const camStarted = ref(false)


let model = undefined;

//loading the model
cocoSsd.load().then(function (loadedModel) {
  model = loadedModel;
  showBtn.value = true
});

async function  enableCam(event) {
    if (!model) {
        return;
      }
      camStarted.value =true;
      const constraints = {
        video: true
      };
    
      // Activate the webcam stream.
     await navigator.mediaDevices.getUserMedia(constraints).then(function(stream) {
      try {
             video.value.srcObject  = stream
              console.log('try',stream)
            } catch (error) {
              console.log('catch',error)
            }
    
        video.value.addEventListener('loadeddata', predictWebcam);
      });
  }


  const children2 = ref([]);
  function predictWebcam() {
    
  //classifying a frame in the stream.
  model.detect(video.value).then(function (predictions) {

    for (let i = 0; i < children2.value.length; i++) {
        onTheScreen.value.removeChild(children2.value[i]);
        
      }

    children2.value.splice(0);
    
    // loop through predictions
    for (let n = 0; n < predictions.length; n++) {

      if (predictions[n].score > 0.66) {
        const div = document.createElement('div');
        div.innerHTML = predictions[n].class 
        //check if prediction class is person to play alarm
        if (predictions[n].class == "person"){
           audio.value.play() 
        }
        onTheScreen.value.appendChild(div);
        children2.value.push(div);

      }
    }
    
    // Call this function again to keep predicting when the browser is ready.
    window.requestAnimationFrame(predictWebcam);
  });
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