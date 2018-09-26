<!--<template>-->
  <!--<div>-->
    <!--&lt;!&ndash;STS上传配置&ndash;&gt;-->
    <!--<hr/>-->
    <!--<table frame=void width="100%">-->
      <!--<tr>-->
        <!--<td>accessKeyId:</td>-->
        <!--<td><input type="text" id="accessKeyId" size="20" value=""></td>-->
        <!--<td>accessKeySecret:</td>-->
        <!--<td><input type="text" id="accessKeySecret" size="40" value=""></td>-->
      <!--</tr>-->
      <!--<tr>-->
        <!--<td>secretToken:</td>-->
        <!--<td><input type="text" id="secretToken" size="40" value=""></td>-->
        <!--<td></td>-->
        <!--<td>点播STS参数如何获取，请查阅<a href=" https://help.aliyun.com/document_detail/28788.html?spm=a2c4g.11186623.2.6.1mSfTK"-->
                              <!--target="_blakn"> 获取STS</a></td>-->
      <!--</tr>-->
      <!--<tr>-->
        <!--<td>endpoint:</td>-->
        <!--&lt;!&ndash; oss-cn-shanghai.aliyuncs.com &ndash;&gt;-->
        <!--&lt;!&ndash; in-20170320144514766-hte7xhqk1t &ndash;&gt;-->
        <!--&lt;!&ndash; video/50659345-16127742b75-0004-a1f7-953-f8a30 &ndash;&gt;-->
        <!--<td><input type="text" id="endpoint" size="40" value=""></td>-->
        <!--<td>bucket:</td>-->
        <!--<td><input type="text" id="bucket" size="20" value=""></td>-->
      <!--</tr>-->
      <!--<tr>-->
        <!--<td>object路径:</td>-->
        <!--<td><input type="text" id="objectPre" size="20" value=""></td>-->

      <!--</tr>-->
      <!--<tr>-->
        <!--<td></td>-->
        <!--<td>以上相关参数如何获取，请查阅<a href="https://help.aliyun.com/document_detail/31867.html?spm=5176.doc31848.2.21.KS9R7q"-->
                             <!--target="_blakn"> OSS访问与控制文档</a></td>-->

      <!--</tr>-->
    <!--</table>-->
    <!--<hr/>-->
    <!--文件管理-->
    <!--<hr/>-->
    <!--<input type="file" name="file" id="files" v-on:change="selectFile($event)" multiple/>-->
    <!--<el-button type="button" @click="clearInputFile">清空继续选择</el-button>-->
    <!--<el-button type="button" @click="deleteFile">删除文件</el-button>-->
    <!--<input type="text" id="deleteIndex" size="3" value="0">-->
    <!--<el-button type="button" @click="cancelFile">取消文件</el-button>-->
    <!--<input type="text" id="cancelIndex" size="3" value="0">-->

    <!--<el-button type="button" @click="resumeFile">恢复文件</el-button>-->
    <!--<input type="text" id="resumeIndex" size="3" value="0">-->
    <!--<hr/>-->
    <!--列表管理-->
    <!--<hr/>-->
    <!--<el-button type="button" @click="getList">获取上传列表</el-button>-->
    <!--<el-button type="button" @click="clearList">清理上传列表</el-button>-->
    <!--<el-button type="button" @click="getCheckpoint">获取断点信息</el-button>-->
    <!--<hr/>-->
    <!--上传管理-->
    <!--<hr/>-->
    <!--<el-button type="button" @click="start" v-if="inited">开始上传</el-button>-->
    <!--<el-button type="button" @click="stop">停止上传</el-button>-->
    <!--&lt;!&ndash;  <button type="button" @click="stop">停止上传</button> &ndash;&gt;-->
    <!--<el-button type="button" @click="resumeWithToken">token过期恢复上传</el-button>-->
    <!--<hr/>-->
    <!--<select multiple="multiple" id="textarea"-->
            <!--style="position:relative; width:90%; height:450px; vertical-align:top; border:1px solid #cccccc;"></select>-->
    <!--<button type="button" @click="clearLog">清日志</button>-->
  <!--</div>-->
<!--</template>-->


<!--<script>-->

  <!--export default {-->
    <!--data() {-->
      <!--return {-->
        <!--inited: false,-->
        <!--videoId: '',-->
        <!--requestId: '',-->
        <!--uploadAddress: '',-->
        <!--uploadAuth: '',-->
        <!--videoForm: {-->
          <!--uploadAuth: '',-->
          <!--uploadAddress: '',-->
          <!--videoId: ''-->
        <!--},-->
        <!--textarea: '',-->
        <!--uploader: null,-->
        <!--prismplayer: ''-->
      <!--}-->
    <!--},-->
    <!--mounted() {-->

    <!--},-->
    <!--methods: {-->
      <!--// 获取数据列表-->
      <!--// getAuth () {-->
      <!--//   console.log(this.videoForm)-->
      <!--//   this.$http({-->
      <!--//     url: this.$http.adornUrl('/sys/vod/createUploadVideo'),-->
      <!--//     method: 'post',-->
      <!--//     params: this.$http.adornParams(this.videoForm)-->
      <!--//   }).then(({data}) => {-->
      <!--//     if (data && data.code === 200) {-->
      <!--//       this.videoId = data.data.videoId-->
      <!--//       this.requestId = data.data.requestId-->
      <!--//       this.uploadAddress = data.data.uploadAddress-->
      <!--//       this.uploadAuth = data.data.uploadAuth-->
      <!--//     } else {-->
      <!--//-->
      <!--//     }-->
      <!--//   })-->
      <!--// },-->

      <!--getCheckpoint() {-->
        <!--let list = this.uploader.listFiles()-->
        <!--for (let i = 0; i < list.length; i++) {-->
          <!--let value = this.uploader.getCheckpoint(list[i].file)-->
          <!--if (value) {-->
            <!--this.log(list[i].file.name + ' checkpoint: videoId=' + value.videoId + ' loaded=' + value.loaded + ' state=' + value.state);-->
          <!--}-->
          <!--else {-->
            <!--this.log(list[i].file.name + ' no checkpoint.')-->
          <!--}-->
        <!--}-->
      <!--},-->

      <!--// 点播上传。每次上传都是独立的鉴权，所以初始化时，不需要设置鉴权-->
      <!--// 临时账号过期时，在onUploadTokenExpired事件中，用resumeWithToken更新临时账号，上传会续传。-->
      <!--selectFile(event) {-->
        <!--let endpoint = document.getElementById('endpoint').value-->
        <!--let bucket = document.getElementById('bucket').value-->
        <!--let objectPre = document.getElementById('objectPre').value-->
        <!--let userData-->
        <!--if (this.isVodMode()) {-->
          <!--userData = '{"Vod":{"StorageLocation":"","UserData":{"IsShowWaterMark":"false","Priority":"7"}}}'-->
        <!--} else {-->
          <!--userData = '{"Vod":{"StorageLocation":"","Title":"this is title.我是标题","Description":"this is desc.我是描述","CateId":"19","Tags":"tag1,tag2,标签3"}}'-->
        <!--}-->
        <!--for (let i = 0; i < event.target.files.length; i++) {-->
          <!--this.log('add file: ' + event.target.files[i].name)-->
          <!--this.videoForm.fileName = event.target.files[i].name-->
          <!--this.videoForm.title = event.target.files[i].name.substring(0, event.target.files[i].name.lastIndexOf('.'))-->
          <!--this.videoForm.cate = 0-->
          <!--this.videoForm.tags = ''-->
          <!--this.$http({-->
            <!--url: this.$http.adornUrl('/sys/vod/createUploadVideo'),-->
            <!--method: 'post',-->
            <!--params: this.$http.adornParams(this.videoForm)-->
          <!--}).then(({data}) => {-->
            <!--if (data && data.code === 200) {-->
              <!--this.videoId = data.data.videoId-->
              <!--this.requestId = data.data.requestId-->
              <!--this.uploadAddress = data.data.uploadAddress-->
              <!--this.uploadAuth = data.data.uploadAuth-->
              <!--this.log('获取到token')-->
              <!--this.getUpload()-->
              <!--// 点播上传。每次上传都是独立的OSS object，所以添加文件时，不需要设置OSS的属性-->
              <!--this.log('开始添加文件')-->
              <!--this.uploader.addFile(event.target.files[i], null, null, null, userData)-->
              <!--this.log('添加哇完毕')-->
              <!--this.inited = true-->
            <!--}-->
          <!--})-->
        <!--}-->
      <!--},-->

      <!--// 上传-->
      <!--getUpload() {-->
        <!--this.log('开始初始化uploader')-->
        <!--this.textarea = document.getElementById('textarea')-->
        <!--const _this = this-->
        <!--_this.uploader = new AliyunUpload.Vod({-->
          <!--// 文件上传失败-->
          <!--'onUploadFailed': function (uploadInfo, code, message) {-->
            <!--_this.log('onUploadFailed: file:' + uploadInfo.file.name + ',code:' + code + ', message:' + message)-->
          <!--},-->
          <!--// 文件上传完成-->
          <!--'onUploadSucceed': function (uploadInfo) {-->
            <!--console.log(uploadInfo)-->
            <!--_this.log('onUploadSucceed: ' + uploadInfo.file.name + ', endpoint:' + uploadInfo.endpoint + ', bucket:' + uploadInfo.bucket + ', object:' + uploadInfo.object)-->
          <!--},-->
          <!--// 文件上传进度-->
          <!--'onUploadProgress': function (uploadInfo, totalSize, loadedPercent) {-->
            <!--_this.log('onUploadProgress:file:' + uploadInfo.file.name + ', fileSize:' + totalSize + ', percent:' + (loadedPercent * 100.00).toFixed(2) + '%')-->
          <!--},-->
          <!--// STS临时账号会过期，过期时触发函数-->
          <!--'onUploadTokenExpired': function (uploadInfo) {-->
            <!--_this.log('onUploadTokenExpired')-->
            <!--if (_this.isVodMode()) {-->
              <!--// 实现时，根据uploadInfo.videoId从新获取UploadAuth-->
              <!--//实际环境中调用点播的刷新上传凭证接口，获取凭证-->
              <!--//https://help.aliyun.com/document_detail/55408.html?spm=a2c4g.11186623.6.630.BoYYcY-->
              <!--//获取上传凭证后，调用setUploadAuthAndAddress-->
              <!--// this.uploader.resumeUploadWithAuth(uploadAuth)-->
            <!--} else if (_this.isSTSMode()) {-->
              <!--// 实现时，从新获取STS临时账号用于恢复上传-->
              <!--// this.uploader.resumeUploadWithSTSToken(accessKeyId, accessKeySecret, secretToken, expireTime)-->
            <!--}-->
          <!--},-->
          <!--onUploadCanceled: function (uploadInfo) {-->
            <!--_this.log('onUploadCanceled:file:' + uploadInfo.file.name)-->
          <!--},-->
          <!--// 开始上传-->
          <!--'onUploadstarted': function (uploadInfo) {-->
            <!--console.log(uploadInfo)-->
            <!--if (_this.isVodMode()) {-->
              <!--if (!uploadInfo.videoId)//这个文件没有上传异常-->
              <!--{-->
                <!--let uploadAuth = _this.uploadAuth-->
                <!--let uploadAddress = _this.uploadAddress-->
                <!--let videoId = _this.videoId-->
                <!--//实际环境中调用调用点播的获取上传凭证接口-->
                <!--//https://help.aliyun.com/document_detail/55407.html?spm=a2c4g.11186623.6.629.CH7i3X-->
                <!--//获取上传凭证后，调用setUploadAuthAndAddress-->
                <!--_this.uploader.setUploadAuthAndAddress(uploadInfo, uploadAuth, uploadAddress, videoId)-->
              <!--} else//如果videoId有值，根据videoId刷新上传凭证-->
              <!--{-->
                <!--//mock 上传凭证 实际产品中需要通过接口获取-->
                <!--let uploadAuth = _this.uploadAuth-->
                <!--let uploadAddress = _this.uploadAddress-->
                <!--//实际环境中调用点播的刷新上传凭证接口，获取凭证-->
                <!--//https://help.aliyun.com/document_detail/55408.html?spm=a2c4g.11186623.6.630.BoYYcY-->
                <!--//获取上传凭证后，调用setUploadAuthAndAddress-->
                <!--_this.uploader.setUploadAuthAndAddress(uploadInfo, uploadAuth, uploadAddress)-->
              <!--}-->
            <!--}-->
            <!--else if (this.isSTSMode()) {-->
              <!--let accessKeyId = document.getElementById('accessKeyId').value-->
              <!--let accessKeySecret = document.getElementById('accessKeySecret').value-->
              <!--let secretToken = document.getElementById('secretToken').value-->
              <!--_this.uploader.setSTSToken(uploadInfo, accessKeyId, accessKeySecret, secretToken)-->
            <!--}-->
            <!--_this.log('onUploadStarted:' + uploadInfo.file.name + ', endpoint:' + uploadInfo.endpoint + ', bucket:' + uploadInfo.bucket + ', object:' + uploadInfo.object)-->
          <!--},-->
          <!--'onUploadEnd': function (uploadInfo) {-->
            <!--_this.log('onUploadEnd: uploaded all the files')-->
          <!--}-->
        <!--})-->
      <!--},-->


      <!--start() {-->
        <!--console.log(this.uploader)-->
        <!--this.log('start upload.')-->
        <!--this.uploader.startUpload()-->
      <!--},-->

      <!--stop() {-->
        <!--this.log('stop upload.')-->
        <!--this.uploader.stopUpload()-->
      <!--},-->

      <!--resumeWithToken() {-->
        <!--this.log('resume upload with token.')-->

        <!--let uploadAuth = this.uploadAuth-->
        <!--let uploadAddress = this.uploadAddress-->
        <!--let videoId = this.videoId-->
        <!--let secretToken = this.secretToken-->


        <!--if (this.isVodMode()) {-->
          <!--this.uploader.resumeUploadWithAuth(uploadAuth)-->
        <!--} else if (this.isSTSMode()) {-->
          <!--this.uploader.resumeUploadWithSTSToken(accessKeyId, accessKeySecret, secretToken)-->
        <!--}-->
      <!--},-->

      <!--clearInputFile() {-->
        <!--let ie = (navigator.appVersion.indexOf('MSIE') != -1);-->
        <!--if (ie) {-->
          <!--let file = document.getElementById('files')-->
          <!--let file2 = file.cloneNode(false);-->
          <!--file2.addEventListener('change', selectFile)-->
          <!--file.parentNode.replaceChild(file2, file);-->
        <!--}-->
        <!--else {-->
          <!--document.getElementById('files').value = ''-->
        <!--}-->

      <!--},-->

      <!--clearList() {-->
        <!--this.log('clean upload list.')-->
        <!--this.uploader.cleanList()-->
      <!--},-->

      <!--getList() {-->
        <!--this.log('get upload list.')-->
        <!--let list = this.uploader.listFiles()-->
        <!--for (let i = 0; i < list.length; i++) {-->
          <!--this.log('file:' + list[i].file.name + ', status:' + list[i].state + ', endpoint:' + list[i].endpoint + ', bucket:' + list[i].bucket + ', object:' + list[i].object)-->
        <!--}-->
      <!--},-->

      <!--deleteFile() {-->
        <!--if (document.getElementById('deleteIndex').value) {-->
          <!--let index = document.getElementById('deleteIndex').value-->
          <!--this.log('delete file index:' + index)-->
          <!--this.uploader.deleteFile(index)-->
        <!--}-->
      <!--},-->

      <!--cancelFile() {-->
        <!--if (document.getElementById('cancelIndex').value) {-->
          <!--let index = document.getElementById('cancelIndex').value-->
          <!--this.log('cancel file index:' + index)-->
          <!--this.uploader.cancelFile(index)-->
        <!--}-->
      <!--},-->

      <!--resumeFile() {-->
        <!--if (document.getElementById('resumeIndex').value) {-->
          <!--let index = document.getElementById('resumeIndex').value-->
          <!--this.log('resume file index:' + index)-->
          <!--this.uploader.resumeFile(index)-->
        <!--}-->
      <!--},-->

      <!--clearLog() {-->
        <!--textarea.options.length = 0-->
      <!--},-->

      <!--log(value) {-->
        <!--if (!value) {-->
          <!--return-->
        <!--}-->

        <!--let len = textarea.options.length-->
        <!--if (len > 0 && textarea.options[len - 1].value.substring(0, 40) == value.substring(0, 40)) {-->
          <!--//textarea.remove(len-1)-->
        <!--} else if (len > 25) {-->
          <!--textarea.remove(0)-->
        <!--}-->

        <!--let option = document.createElement('option')-->
        <!--option.value = value, option.innerHTML = value-->
        <!--textarea.appendChild(option)-->
      <!--},-->

      <!--isVodMode() {-->
        <!--return (this.uploadAuth && this.uploadAuth.length > 0)-->
      <!--},-->

      <!--isSTSMode() {-->
        <!--let secretToken = this.secretToken-->
        <!--if (!this.isVodMode()) {-->
          <!--if (secretToken && secretToken.length > 0) {-->
            <!--return true-->
          <!--}-->
        <!--}-->
        <!--return false-->
      <!--}-->

    <!--}-->
  <!--}-->
<!--</script>-->
