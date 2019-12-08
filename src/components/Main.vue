<template>

    <div id="mainComponent">
        <!--        Need to figure out full screen-->
        <div class="videoContainer" id="videoContainer">
            <video muted id="video" width="100%" height="auto" autoplay></video>
        </div>

        <div class="header">
            <h1>326 Project</h1>
        </div>

    </div>

</template>




<script>
    /* eslint-disable no-console */
    export default {
        name: 'Main',
        mounted: function () {
            this.$nextTick(function () {
                let video = document.querySelector("video");
                Promise.all([
                    faceapi.nets.faceRecognitionNet.loadFromUri('/models'),
                    faceapi.nets.faceLandmark68Net.loadFromUri('/models'),
                    faceapi.nets.ssdMobilenetv1.loadFromUri('/models')
                ]).then(startVideo);

                function startVideo() {
                    navigator.getUserMedia(
                        { video: {} },
                        stream => video.srcObject = stream,
                        err => console.error(err)
                    )
                }

                video.addEventListener('play', async () => {
                    let container = document.getElementById("mainComponent");
                    const labeledFaceDescriptors = await loadLabeledImages();
                    const faceMatcher = new faceapi.FaceMatcher(labeledFaceDescriptors, 0.6);
                    let canvas;
                    canvas = faceapi.createCanvasFromMedia(video);
                    container.append(canvas);
                    const displaySize = {width: video.offsetWidth, height: video.offsetHeight};
                    faceapi.matchDimensions(canvas, displaySize);
                    setInterval(async () => {
                        const singleResult = await faceapi
                            .detectSingleFace(video)
                            .withFaceLandmarks()
                            .withFaceDescriptor();

                        if (singleResult) {
                            const bestMatch = faceMatcher.findBestMatch(singleResult.descriptor);
                            console.log(bestMatch.toString())
                        }
                        if (singleResult) {
                            const bestMatch = faceMatcher.findBestMatch(singleResult.descriptor);
                            console.log(bestMatch.toString())
                        }
                        // if (detection){
                        //     const resizedDetection = faceapi.resizeResults(detection, displaySize);
                        //     const result = faceMatcher.findBestMatch(detection.descriptor);
                        //     const box = resizedDetection.detection.box;
                        //     const drawBox = new faceapi.draw.DrawBox(box, {label: result.toString()});
                        //     drawBox.draw(canvas);
                        // }
                    }, 1000);
                });

                function loadLabeledImages() {
                    //const labels = ['Andrew_Smith', 'Thivakkar_Mahendran', 'Akhil_Raheja', 'Rishi_Jambunathan']
                    const labels = ['Andrew_Smith'];
                    //const labels = ['Black Widow', 'Captain America', 'Captain Marvel', 'Hawkeye', 'Jim Rhodes', 'Thor', 'Tony Stark'];
                    return Promise.all(
                        labels.map(async label => {
                            const descriptions = [];
                            for (let i = 1; i <= 2; i++) {
                                const img = await faceapi.fetchImage(`http://s3.amazonaws.com/labeledimage/${label}/${i}.jpg`);
                                //const img = await faceapi.fetchImage(`https://raw.githubusercontent.com/WebDevSimplified/Face-Recognition-JavaScript/master/labeled_images/${label}/${i}.jpg`)
                                const detections = await faceapi.detectSingleFace(img).withFaceLandmarks().withFaceDescriptor();
                                descriptions.push(detections.descriptor);
                            }

                            return new faceapi.LabeledFaceDescriptors(label, descriptions);
                        })
                    )
                }
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

    .header {
        padding: 1px;
        text-align: center;
        background: #1abc9c;
        color: white;
        font-size: 12px;
        z-index:1000;
        position:relative;
    }

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
        object-fit: cover;
    }

    * {
        margin: 0;
        padding: 0;
    }

    html {
        min-height: 100%;
        overflow-y: scroll;
    }
    body {
        min-height: 100%;
    }


</style>
