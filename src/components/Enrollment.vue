<template>
    <div>
        <video muted id="video" width="100%" height="100%" autoplay></video>
    </div>
</template>

<script>
    /* eslint-disable no-console */
    export default {
        name: 'Enrollment'
    }
    // Prefer camera resolution nearest to 1280x720.
    var constraints = { audio: false, video: { width: 1280, height: 720 } };

    navigator.mediaDevices.getUserMedia(constraints)
        .then(function(mediaStream) {
            var video = document.querySelector('video');
            video.srcObject = mediaStream;
            video.onloadedmetadata = function() {
                video.play();
            };
        })
        .catch(function(err) { console.log(err.name + ": " + err.message); }); // always check for errors at the end.

</script>

<style scoped>

</style>
