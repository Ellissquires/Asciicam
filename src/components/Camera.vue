<template>
<div v-if="error == null">
  <p v-if="stream === null">
    Please allow access to your camera when prompted
  </p>

  <video v-bind:src="stream" class="video" v-if="stream" autoplay v-el:video ></video>
  <canvas class="canvas" v-el:canvas> </canvas>
</div>
<div v-else>
  {{ error }}
</div>
</template>

<script>


export default {
  data () {
    return {
        error : null,
        stream: null,
        context: null,
        ascii: {
          contrast: 30,
          fps: 30
        }
      }
    },
    methods : {
      _initVideo() {
        navigator.getUserMedia({
          video: true,
          audio: false
        }, (stream)=> {
          this.stream = window.URL.createObjectURL(stream)

          this._bootCanvas().then(() => {
            this._startAsciiRendering()
          })
        }, this._setError)
      },

      _startAsciiRendering() {
        setInterval(() => {
          this.context.drawImage(this.$els.video,
            0,
            0,
            this.$els.canvas.width,
            this.$els.canvas.height
          )
        }, Math.round(1000/ this.ascii.fps))
      },
      _bootCanvas(){

        return new Promise((resolve, reject) =>{
          this.context = this.$els.canvas.getContext('2d')
          this.$els.canvas.width = 200
          this.$els.canvas.height = 150

          resolve()
        })

      },
      _setUnsupported() {
        this.error = "Your browser does not support video";
      },
      _setError() {
        this.error = "Something went wrong";
      },
    },

    ready() {
      if(navigator.getUserMedia){
        this._initVideo()

      }else {
        this._setUnsupported()
      }
    }
  }

</script>

<style scoped lang="sass">
html, body
  width: 100%
  height: 100%
  margin: 0
  padding: 0

.video
  width: 200px
  position: fixed
  top: 0
  left: 0
  z-index: 1
.canvas
  display: none
</style>
