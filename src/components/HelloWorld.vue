<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <input type="file" accept="video/*" capture >
    <video id="myVideo" class="video-js vjs-default-skin" playsinline></video>
  </div>
</template>

<script>
import 'video.js/dist/video-js.css'
import 'videojs-record/dist/css/videojs.record.css'

import 'webrtc-adapter'
import RecordRTC from 'recordrtc'

import videojs from 'video.js'
// eslint-disable-next-line
import Record from 'videojs-record/dist/videojs.record.js'

export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },
  data () {
    return {
      player: '',
      options: {
        controls: true,
        autoplay: false,
        fluid: false,
        loop: false,
        width: 320,
        height: 240,
        controlBar: {
          volumePanel: false
        },
        plugins: {
          // configure videojs-record plugin
          record: {
            audio: false,
            video: true,
            debug: true
          }
        }
      }
    }
  },
  methods: {
  },
  mounted () {
    /* eslint-disable no-console */
    this.player = videojs('#myVideo', this.options, () => {
      // print version information at startup
      var msg = 'Using video.js ' + videojs.VERSION +
                    ' with videojs-record ' + videojs.getPluginVersion('record') +
                    ' and recordrtc ' + RecordRTC.version
      videojs.log(msg)
    })

    // device is ready
    this.player.on('deviceReady', () => {
      console.log('device is ready!')
    })

    // user clicked the record button and started recording
    this.player.on('startRecord', () => {
      console.log('started recording!')
    })

    // user completed recording and stream is available
    this.player.on('finishRecord', () => {
      // the blob object contains the recorded data that
      // can be downloaded by the user, stored on server etc.
      console.log('finished recording: ', this.player.recordedData)
      if (this.player) {
        this.player.record().stopDevice()
      }
    })

    // error handling
    this.player.on('error', (element, error) => {
      console.warn(error)
    })

    this.player.on('deviceError', () => {
      console.error('device error:', this.player.deviceErrorCode)
    })
  },
  beforeDestroy () {
    if (this.player) {
      this.player.dispose()
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
