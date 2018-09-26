<template>
  <div>
    <input type="file" name="" id="files" v-on:change="selectFile($event)" multiple/>
    <span v-show="inited">
      <!--<el-button @click="deleteFile" size="mini" class="deleteVideo" v-if="finishUpload">清空选择</el-button>-->
        <el-progress :percentage="percentage" color="#8e71c7" style="width: 100%;"></el-progress>
      <input type="text" id="deleteIndex" size="3" value="0" v-show="">
      <el-button type="primary" @click="start">开始上传</el-button>
      <el-button type="button" @click="stop" v-if="stopUpload">停止</el-button>
    </span>
  </div>
</template>


<script>
  export default {
    data() {
      return {
        finishUpload: true,
        inited: false,
        stopUpload: false,
        percentage: 0,
        del: false,
        videoId: '',
        requestId: '',
        uploadAddress: '',
        uploadAuth: '',
        textarea: '',
        uploader: null,
        prismplayer: '',
        videoForm: {
          fileName: '',
          title: '',
          cate: '',
          tags: ''
        }
      }
    },
    mounted() {

    },
    methods: {
      // 选择文件
      selectFile(event) {
        this.percentage = 0
        let userData
        if (this.isVodMode()) {
          userData = '{"Vod":{"StorageLocation":"","UserData":{"IsShowWaterMark":"false","Priority":"7"}}}'
        } else {
          userData = '{"Vod":{"StorageLocation":"","Title":"this is title.我是标题","Description":"this is desc.我是描述","CateId":"19","Tags":"tag1,tag2,标签3"}}'
        }
        for (let i = 0; i < event.target.files.length; i++) {
          console.log('add file: ' + event.target.files[i].name)
          this.videoForm.fileName = event.target.files[i].name
          this.videoForm.title = event.target.files[i].name.substring(0, event.target.files[i].name.lastIndexOf('.'))
          this.videoForm.cate = 0
          this.videoForm.tags = ''
          this.del = true
          this.$http({
            url: this.$http.adornUrl('/sys/vod/createUploadVideo'),
            method: 'post',
            params: this.$http.adornParams(this.videoForm)
          }).then(({data}) => {
            if (data && data.code === 200) {
              this.videoId = data.data.videoId
              this.requestId = data.data.requestId
              this.uploadAddress = data.data.uploadAddress
              this.uploadAuth = data.data.uploadAuth
              this.getUpload()
              // 添加文件
              this.uploader.addFile(event.target.files[i], null, null, null, userData)
              console.log('添加完毕')
              this.stopUpload = true
              this.inited = true
            } else {
              this.$message.danger('获取参数异常')
            }
          })
        }
      },
      // 初始化上传以及回调函数
      getUpload() {
        console.log('可以上传了')
        const _this = this
        _this.uploader = new AliyunUpload.Vod({
          // 文件上传失败
          'onUploadFailed': function (uploadInfo, code, message) {
            console.log('onUploadFailed: file:' + uploadInfo.file.name + ',code:' + code + ', message:' + message)
          },
          // 文件上传完成
          'onUploadSucceed': function (uploadInfo) {
            _this.$emit('successCBK', uploadInfo)
            console.log(uploadInfo)
            console.log('onUploadSucceed: ' + uploadInfo.file.name + ', endpoint:' + uploadInfo.endpoint + ', bucket:' + uploadInfo.bucket + ', object:' + uploadInfo.object)
            alert('上传成功')
            this.$message.error(data.msg)
          },
          // 文件上传进度
          'onUploadProgress': function (uploadInfo, totalSize, loadedPercent) {
            _this.percentage = parseInt((loadedPercent * 100).toFixed(2))
            console.log('onUploadProgress:file:' + uploadInfo.file.name + ', fileSize:' + totalSize + ', percent:' + (loadedPercent * 100.00).toFixed(2) + '%')
          },
          // STS临时账号会过期，过期时触发函数
          'onUploadTokenExpired': function (uploadInfo) {
            console.log('onUploadTokenExpired')
            this.uploader.resumeUploadWithAuth(uploadAuth)
          },
          onUploadCanceled: function (uploadInfo) {
            console.log('onUploadCanceled:file:' + uploadInfo.file.name)
          },
          // 开始上传
          'onUploadstarted': function (uploadInfo) {
            if (!uploadInfo.videoId)//这个文件没有上传异常
            {
              let uploadAuth = _this.uploadAuth
              let uploadAddress = _this.uploadAddress
              let videoId = _this.videoId
              _this.uploader.setUploadAuthAndAddress(uploadInfo, uploadAuth, uploadAddress, videoId)
            } else//如果videoId有值，根据videoId刷新上传凭证
            {
              let uploadAuth = _this.uploadAuth
              let uploadAddress = _this.uploadAddress
              _this.uploader.setUploadAuthAndAddress(uploadInfo, uploadAuth, uploadAddress)
            }
            console.log('onUploadStarted:' + uploadInfo.file.name + ', endpoint:' + uploadInfo.endpoint + ', bucket:' + uploadInfo.bucket + ', object:' + uploadInfo.object)
          },
          'onUploadEnd': function (uploadInfo) {
            this.inited = false
          }
        })
      },

      // 开始上传
      start () {
        console.log(this.uploader)
        this.uploader.startUpload()
      },
      // 停止上传
      stop () {
        this.uploader.stopUpload()
      },
      deleteFile () {
        document.getElementById('files').value = ''
        if (document.getElementById('deleteIndex').value) {
          let index = document.getElementById('deleteIndex').value
          console.log('delete file index:' + index)
          this.uploader.deleteFile(index)
        }
        this.inited = false
        this.percentage = 0
      },
      // 验证得到授权
      isVodMode () {
        return (this.uploadAuth && this.uploadAuth.length > 0)
      }
    }
  }
</script>
