<template>
    <div>
        <video muted id="video" width="100%" height="100%" autoplay></video>
        <canvas id="frame" width="640" height="480"></canvas>
    </div>
</template>

<script>
    /* eslint-disable no-console */
    export default {
        name: 'Enrollment'
    }
    // Prefer camera resolution nearest to 1280x720.
    var video;
    var constraints = { audio: false, video: { width: 1280, height: 720 } };


    navigator.mediaDevices.getUserMedia(constraints)
        .then(function(mediaStream) {
            video = document.querySelector('video');
            video.srcObject = mediaStream;
            video.onloadedmetadata = function() {
                video.play();
            };
        })
        .catch(function(err) { console.log(err.name + ": " + err.message); }); // always check for errors at the end.

    //---------------------
    // TAKE A SNAPSHOT CODE
    //---------------------

    // implements nodejs wrappers for HTMLCanvasElement, HTMLImageElement, ImageData

    import * as canvas from 'canvas';

    import * as faceapi from 'face-api.js';
    // // patch nodejs environment, we need to provide an implementation of
    // // HTMLCanvasElement and HTMLImageElement, additionally an implementation
    // // of ImageData is required, in case you want to use the MTCNN

    var frame, ctx;
    const { Canvas, Image, ImageData } = canvas;
    const MODEL_URL = './assets/models';

    faceapi.env.monkeyPatch({ Canvas, Image, ImageData });


    window.setInterval(function(){
       snapshot();
    }, 1000);

    async function snapshot() {
        frame = document.getElementById("frame");
        ctx = frame.getContext('2d');
        // Draws current image from the video element into the canvas
        ctx.drawImage(video, 0,0, frame.width, frame.height);

        await faceDetectionNet.loadFromDisk('../../weights');
        await faceapi.nets.faceLandmark68Net.loadFromDisk('../../weights');
        await faceapi.nets.faceRecognitionNet.loadFromDisk('../../weights');

        const resultsRef = await faceapi.detectAllFaces(frame, faceDetectionOptions)
            .withFaceLandmarks()
            .withFaceDescriptors()

    }
</script>

<style scoped>

</style>
