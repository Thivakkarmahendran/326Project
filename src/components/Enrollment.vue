<template>
    <div>
        <h2>Snapshot:</h2>
        <img :src="snapshotData">
    </div>
</template>

<script>
    /* eslint-disable no-console */
    export default {
        name: 'Enrollment',
        data() {
            return {
                snapshotData: ''
            }
        }
    }

    var NodeWebcam = require( "node-webcam" );

    //Default options

    var opts = {

        //Picture related
        width: 1280,
        height: 720,
        quality: 100,
        //Delay in seconds to take shot
        //if the platform supports miliseconds
        //use a float (0.1)
        //Currently only on windows
        delay: 0,
        //Save shots in memory
        saveShots: true,
        // [jpeg, png] support varies
        // Webcam.OutputTypes
        output: "jpeg",
        //Which camera to use
        //Use Webcam.list() for results
        //false for default device
        device: false,
        // [location, buffer, base/64]
        // Webcam.CallbackReturnTypes
        callbackReturn: "location",
        //Logging
        verbose: false
    };
    NodeWebcam.capture( "test_picture", opts, function( err, data ) {

        this.snapshotData = data;

    });

</script>

<style scoped>

</style>
