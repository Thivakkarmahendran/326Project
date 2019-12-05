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
    //import * as faceapi from "../assets/face-api.min";

    // implements nodejs wrappers for HTMLCanvasElement, HTMLImageElement, ImageData
    //import * as canvas from 'canvas';

    import * as faceapi from 'face-api.js';

    // patch nodejs environment, we need to provide an implementation of
    // HTMLCanvasElement and HTMLImageElement, additionally an implementation
    // of ImageData is required, in case you want to use the MTCNN
    //const { Canvas, Image, ImageData } = canvas
    //faceapi.env.monkeyPatch({ Canvas, Image, ImageData })

    // Promise.all([
    //     faceapi.nets.tinyFaceDetector.loadFromUri('/models'),
    //     faceapi.nets.faceLandmark68Net.loadFromUri('/models'),
    //     faceapi.nets.faceRecognitionNet.loadFromUri('/models'),
    //     faceapi.nets.faceExpressionNet.loadFromUri('/models')
    // ]).then(startLiveVideo);

    loadModels = async () => {
        const MODEL_URL = "/models";
        await faceapi.loadSsdMobilenetv1Model(MODEL_URL);
        await faceapi.loadFaceLandmarkModel(MODEL_URL);
        await faceapi.loadFaceRecognitionModel(MODEL_URL);
    };
    startLiveVideo();


    function startLiveVideo() {
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
                video.addEventListener('play', () => {
                    const canvas = faceapi.createCanvasFromMedia(video);
                    document.body.append(canvas);
                    //NEED TO FIGURE OUT DYNAMIC HEIGHT AND WIDTH
                    const displaySize = { width: "1280", height: "720" };
                    faceapi.matchDimensions(canvas, displaySize);
                    setInterval(async () => {
                        const detections = await faceapi.detectAllFaces(video, new faceapi.TinyFaceDetectorOptions()).withFaceLandmarks().withFaceExpressions();
                        const resizedDetections = faceapi.resizeResults(detections, displaySize);
                        canvas.getContext('2d').clearRect(0, 0, canvas.width, canvas.height);
                        faceapi.draw.drawDetections(canvas, resizedDetections);
                        faceapi.draw.drawFaceLandmarks(canvas, resizedDetections);
                        faceapi.draw.drawFaceExpressions(canvas, resizedDetections);
                    }, 300)
                });
            })
            .catch(function(err) { console.log(err.name + ": " + err.message); });
    }

</script>

<style scoped>
    body {
        margin: 0;
        padding: 0;
        /*width: 100vw;*/
        /*height: 100vh;*/
        display: flex;
        justify-content: center;
        align-items: center;
    }
    canvas {
        position: absolute;
    }
</style>
