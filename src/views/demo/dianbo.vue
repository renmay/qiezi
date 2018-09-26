<template>
  <div class="">
    <div class="prism-player" id="J_prismPlayer" style="position: absolute"></div>
  </div>
</template>
<style>
</style>

<script>
  export default {
    data () {
      return {
        prismplayer: ''
      }
    },
    mounted: function() {
      let player = new Aliplayer({
        id: 'J_prismPlayer',
        width: '100%',
        autoplay: false,
        prismType: 2,
        vid: '',
        accId: '',
        accSecret: '',
        apiKey: '',
        showBarTime: 3000000,
        source: 'http://cloud.video.taobao.com/play/u/2554695624/p/1/e/6/t/1/fv/102/28552077.mp4',
        cover: 'http://localhost:8001/static/img/avatar.66c34bd.jpg',
        height: '450px',
        playsinline: true,
        seekable: true,
        skinLayout: [{
          'name': 'bigPlayButton',
          'align': 'cc'
        }, {
          'align': 'blabs',
          'x': 0,
          'y': 0,
          'name': 'controlBar',
          'children': [
            {
              'align': 'tl',
              'x': 15,
              'y': 26,
              'name': 'playButton'
            }, {
              'align': 'tl',
              'x': 10,
              'y': 24,
              'name': 'timeDisplay'
            }, {
              'align': 'tr',
              'x': 20,
              'y': 25,
              'name': 'fullScreenButton'
            }, {
              'align': 'tr',
              'x': 20,
              'y': 25,
              'name': 'volume'
            }, {
              'name': 'progress',
              'align': 'tlabs',
              'x': 0,
              'y': 0
            }
          ]
        }]
      })
    }
  }
</script>
