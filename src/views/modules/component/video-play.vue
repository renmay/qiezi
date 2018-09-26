<template>
  <div class="ali-player" ref="container">
    <!--<el-button @click="getVideoPlayAuth">获取播放凭证</el-button>-->
    <div class="prism-player" id="J_prismPlayer" style="width: 90%;height: 220px;"></div>
  </div>
</template>
<style>
</style>

<script>
  export default {
    data() {
      return {
        dataForm:{
          palyauth: '',
          cover: '',
          vid: '',
        },
        playInfoList: []
      }
    },
    mounted: function () {
      this.initPlayer ()
    },
    methods: {
      // 初始化player
      initPlayer (temp) {
        const vid = temp.vid
        const playAuth = temp.playAuth
        const cover = temp.cover
        let player = new Aliplayer({
          id: 'J_prismPlayer',
          width: '100%',
          autoplay: false,
          //播放方式二：点播用户推荐
          vid : vid,
          playauth :  playAuth,
          cover: cover
        })
      },
      // 获取播放凭证
      getVideoPlayAuth(videoId) {
        this.$http({
          url: this.$http.adornUrl('/sys/vod/getVideoPlayAuth'),
          method: 'post',
          params: this.$http.adornParams({
            videoId: videoId
          })
        }).then(({data}) => {
          if (data && data.code === 200) {
            console.log(data.data)
            // 不能直接操作属性 why
            let temp = {}
            temp.playAuth = data.data.playAut
            temp.cover = data.data.videoMeta.coverURL
            temp.vid = videoId
            this.dataForm = temp
            console.log(temp)
            this.initPlayer(temp)
          } else {
            this.$message.error(data.msg)
          }
        })
      },
      // 获取播放url
      getVideoPlayUrl() {
        this.$http({
          url: this.$http.adornUrl('/sys/vod/getVideoPlayUrl'),
          method: 'post',
          params: this.$http.adornParams({
            videoId: '59ca468b055149809edf0c6157ee27fa'
          })
        }).then(({data}) => {
          if (data && data.code === 200) {
            console.log(data)
            console.log(data.data.videoBase.coverURL)
            for (let i = 0; i < data.data.playInfoList.length; i++) {
              console.log(data.data.playInfoList[0].playURL)
              this.dataForm.source = data.data.playInfoList[0].playURL
            }
            console.log(data.data.playInfoList.length)
            this.inited = true
          } else {
            console.log('获取参数异常')
          }
        })
      },
      close () {
        this.$refs.container.background('white')
      }
    }
  }
</script>
