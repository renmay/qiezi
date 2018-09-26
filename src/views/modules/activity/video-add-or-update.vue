<template>
  <div>
    <el-dialog
      :title="!dataForm.id ? '新增' : '修改'"
      :close-on-click-modal="false"
      :visible.sync="visible">
      <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()"
               label-width="80px">
        <el-form-item label="活动名称" prop="title">
          <el-input v-model="dataForm.title" placeholder=""></el-input>
        </el-form-item>
        <el-form-item label="视频url" prop="url">
          <el-input v-model="dataForm.url" placeholder=""></el-input>
        </el-form-item>
        <el-form-item label="视频" prop="url" v-if="containerVisible">
            <div style="width: 100%;height: 220px;">
              <video-play ref="player"></video-play>
            </div>
        </el-form-item>
        <el-form-item label="修改视频" prop="url">
          <video-upload @successCBK="uploadSucceed"></video-upload>
        </el-form-item>
        <el-form-item label="videoId">
          <el-input v-model="dataForm.videoId" placeholder=""></el-input>
        </el-form-item>
        <el-form-item label="用户id" prop="memberId">
          <el-input v-model="dataForm.memberId" placeholder=""></el-input>
        </el-form-item>
        <el-form-item label="类型" size="mini" prop="type" disabled>
          <el-radio-group v-model="dataForm.type">
            <el-radio :label="1">个人</el-radio>
            <el-radio :label="2">战队</el-radio>
          </el-radio-group>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
    </el-dialog>
  </div>
</template>

<script>
  import VideoUpload from '../component/video-upload'
  import VideoPlay from '../component/video-play'

  export default {
    data: function () {
      return {
        tempData: {},
        containerVisible: true,
        value: '',
        visible: false,
        dataForm: {
          id: '',
          memberId: '',
          status: '',
          teamId: '',
          title: '',
          type: '',
          createTime: '',
          url: '',
          videoId: ''
        },
        dataRule: {
          url: [
            {required: true, message: '不能为空', trigger: 'blur'}
          ],
          title: [
            {required: true, message: '不能为空', trigger: 'blur'}
          ]
        }
      }
    },
    components: {
      VideoUpload,
      VideoPlay
    },
    created (){
    },
    methods: {
      // 获取表单数据
      init (id) {
        this.dataForm.id = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/video?id=${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 200) {
                this.dataForm.title = data.data.title
                this.dataForm.createTime = data.data.createTime
                this.dataForm.id = data.data.id
                this.dataForm.status = data.data.status
                this.dataForm.teamId = data.data.teamId
                this.dataForm.memberId = data.data.memberId
                this.dataForm.title = data.data.title
                this.dataForm.type = data.data.type
                this.dataForm.url = data.data.url
                this.dataForm.videoId = data.data.videoId
                this.$refs.player.getVideoPlayAuth(this.dataForm.videoId)
              }
            })
          }
        })
      },
      // 视频上传成功的回调
      uploadSucceed (uploadInfo) {
        let tempData = this.dataForm
        tempData.object = uploadInfo.object
        tempData.videoId = uploadInfo.videoId
        tempData.endpoint = uploadInfo.endpoint
        this.dataForm = tempData
        console.info('上传成功===================')
        this.containerVisible = false
      },
      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/video/edit`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'url': this.dataForm.url,
                'title': this.dataForm.title,
                'activityId': this.dataForm.activityId,
                'memberId': this.dataForm.memberId,
                'teamId': this.dataForm.teamId,
                'type': this.dataForm.type,
                'notice': this.dataForm.notice,
                'videoId': this.dataForm.videoId
              })
            }).then(({data}) => {
              if (data && data.code === 200) {
                this.$message({
                  message: '操作成功',
                  type: 'success',
                  duration: 1500,
                  onClose: () => {
                    this.visible = false
                    this.$emit('refreshDataList')
                  }
                })
              } else {
                this.$message.error(data.msg)
              }
            })
          }
        })
        this.reload()
      }
    }
  }
</script>
