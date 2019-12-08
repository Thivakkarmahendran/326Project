<template>
    <div id="mainComponent">
        <!--        Need to figure out full screen-->
        <div class="videoContainer" id="videoContainer">
            <video muted id="video" width="100%" height="auto" autoplay></video>
        </div>
    </div>
</template>

<script>
    /* eslint-disable no-console */
    export default {
        name: 'Main',
        mounted: function () {
            this.$nextTick(function () {
                // Code that will run only after the
                // entire view has been rendered
                let video = document.querySelector('video');

                Promise.all([
                    faceapi.nets.tinyFaceDetector.loadFromUri('/models'),
                    faceapi.nets.faceLandmark68Net.loadFromUri('/models'),
                    faceapi.nets.faceRecognitionNet.loadFromUri('/models'),
                    faceapi.nets.faceExpressionNet.loadFromUri('/models')
                ]).then(startVideo);

                function startVideo() {
                    navigator.getUserMedia(
                        { video: {} },
                        stream => video.srcObject = stream,
                        err => console.error(err)
                    )
                }

                video.addEventListener('play', () => {
                    var parentDiv = document.getElementById('mainComponent');
                    const canvas = faceapi.createCanvasFromMedia(video);
                    parentDiv.append(canvas);
                    //document.body.append(canvas);
                    const displaySize = { width: video.offsetWidth, height: video.offsetHeight };
                    faceapi.matchDimensions(canvas, displaySize);
                    setInterval(async () => {
                        //const detections = await faceapi.detectAllFaces(video, new faceapi.TinyFaceDetectorOptions()).withFaceLandmarks().withFaceExpressions();

                    }, 100)
                })
            })
        }
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
    //
    // function startLiveVideo() {
    //     const constraints = {audio: false, video: {width: 1280, height: 720}};
    //
    //
    //     navigator.mediaDevices.getUserMedia(constraints)
    //         .then(function(mediaStream) {
    //             // Prefer camera resolution nearest to 1280x720.
    //             videoFeed.srcObject = mediaStream;
    //             videoFeed.onloadedmetadata = function() {
    //                 videoFeed.play();
    //             };
    //
    //             //I DONT LIKE THIS HERE
    //             videoFeed.addEventListener('play', () => {
    //                 // Prefer camera resolution nearest to 1280x720.
    //                 const videoFeed = document.getElementById('video');
    //                 var videoParentDiv = document.getElementById('mainComponent');
    //
    //                 const canvas = faceapi.createCanvasFromMedia(videoFeed);
    //                 //document.body.append(canvas);
    //                 videoFeed.videoParentDiv.insertBefore(canvas, videoFeed.nextSibling);
    //                 //NEED TO FIGURE OUT DYNAMIC HEIGHT AND WIDTH
    //                 const displaySize = { width: videoFeed.width, height: videoFeed.height };
    //                 faceapi.matchDimensions(canvas, displaySize);
    //                 setInterval(async () => {
    //                     const detections = await faceapi.detectSingleFace(videoFeed, new faceapi.TinyFaceDetectorOptions()).withFaceLandmarks().withFaceExpressions();
    //                     if (detections) {
    //                         const resizedDetections = faceapi.resizeResults(detections, displaySize);
    //                         canvas.getContext('2d').clearRect(0, 0, canvas.width, canvas.height);
    //                         faceapi.draw.drawDetections(canvas, resizedDetections);
    //                         faceapi.draw.drawFaceLandmarks(canvas, resizedDetections);
    //                         faceapi.draw.drawFaceExpressions(canvas, resizedDetections);
    //                     }
    //                 }, 300)
    //             });
    //         })
    //         .catch(function(err) { console.log(err.name + ": " + err.message); });
    // }

</script>

<style scoped>
    .videoContainer
    {
        top: 0;
        left: 0;
        height:100%;
        width:100%;
        position: absolute;
        overflow: hidden;
        z-index: 1;
    }

    .videoContainer video
    {
        min-width: 100%;
        min-height: 100%;
    }
</style>
