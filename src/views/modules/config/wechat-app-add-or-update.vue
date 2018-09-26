<template>
  <div>
    <el-dialog
      :title="!dataForm.key ? '修改' : '修改'"
      :close-on-click-modal="false"
      :visible.sync="visible">
      <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()"
               label-width="120px">
        <el-form-item label="key" prop="key">
          <el-input v-model="dataForm.key" placeholder=""></el-input>
        </el-form-item>
        <el-form-item label="aesKey" prop="aesKey">
          <el-input v-model="dataForm.aesKey" placeholder=""></el-input>
        </el-form-item>
        <el-form-item label="secret" prop="secret">
          <el-input v-model="dataForm.secret" placeholder=""></el-input>
        </el-form-item>
        <el-form-item label="appId" prop="appId">
          <el-input v-model="dataForm.appId" placeholder=""></el-input>
        </el-form-item>
        <el-form-item label="token" prop="token">
          <el-input v-model="dataForm.token" placeholder=""></el-input>
        </el-form-item>
        <el-form-item label="商户号" prop="mchId">
          <el-input v-model="dataForm.mchId" placeholder=""></el-input>
        </el-form-item>
        <el-form-item label="证书路径" prop="certPath">
          <el-input v-model="dataForm.certPath" placeholder=""></el-input>
        </el-form-item>
        <el-form-item label="noticeUrl" prop="noticeUrl">
          <el-input v-model="dataForm.noticeUrl" placeholder=""></el-input>
        </el-form-item>
        <el-form-item label="子商户appid" prop="subAppId">
          <el-input v-model="dataForm.subAppId" placeholder=""></el-input>
        </el-form-item>
        <el-form-item label="子商户商户号" prop="subMchId">
          <el-input v-model="dataForm.subMchId" placeholder=""></el-input>
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
  import ImageUpload from '../component/image-upload'

  export default {
    data: function () {
      return {
        value: '',
        visible: false,
        dataForm: {
          key: '',
          aesKey: '',
          secret: '',
          appId: '',
          token: '',
          mchId: '',
          certPath: '',
          noticeUrl: '',
          subAppId: '',
          subMchId: ''
        },
        dataRule: {
          key: [
            {required: true, message: '不能为空', trigger: 'blur'}
          ],
          aesKey: [
            {required: true, message: '不能为空', trigger: 'blur'}
          ],
          secret: [
            {required: true, message: '不能为空', trigger: 'blur'}
          ],
          appId: [
            {required: true, message: '不能为空', trigger: 'blur'}
          ],
          token: [
            {required: true, message: '不能为空', trigger: 'blur'}
          ],
          mchId: [
            {required: true, message: '不能为空', trigger: 'blur'}
          ],
          certPath: [
            {required: true, message: '不能为空', trigger: 'blur'}
          ],
          noticeUrl: [
            {required: true, message: '不能为空', trigger: 'blur'}
          ],
          subAppId: [
            {required: true, message: '不能为空', trigger: 'blur'}
          ],
          subMchId: [
            {required: true, message: '不能为空', trigger: 'blur'}
          ]
        }
      }
    },
    components: {
      ImageUpload
    },
    methods: {
      // 获取表单数据
      init () {
        this.visible = true
        this.$http({
          url: this.$http.adornUrl(`/third/account/config/weixin/app`),
          method: 'get'
        }).then(({data}) => {
          if (data && data.code === 200) {
            console.log(data)
            this.dataForm.key = data.data.key
            this.dataForm.aesKey = data.data.aesKey
            this.dataForm.secret = data.data.secret
            this.dataForm.appId = data.data.appId
            this.dataForm.token = data.data.token
            this.dataForm.mchId = data.data.mchId
            this.dataForm.certPath = data.data.certPath
            this.dataForm.noticeUrl = data.data.noticeUrl
            this.dataForm.subAppId = data.data.subAppId
            this.dataForm.subMchId = data.data.subMchId
          }
        })
      },

      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/third/account/config/weixin/app`),
              method: 'post',
              data: this.$http.adornData({
                'key': this.dataForm.key,
                'aesKey': this.dataForm.aesKey,
                'secret': this.dataForm.secret,
                'appId': this.dataForm.appId,
                'token': this.dataForm.token,
                'mchId': this.dataForm.mchId,
                'certPath': this.dataForm.certPath,
                'noticeUrl': this.dataForm.noticeUrl,
                'subAppId': this.dataForm.subAppId,
                'subMchId': this.dataForm.subMchId
              })
            }).then(({data}) => {
              if (data && data.code === 200) {
                this.$message({
                  message: '操作成功',
                  type: 'success',
                  duration: 1500,
                  onClose: () => {
                    this.visible = false
                  }
                })
              } else {
                this.$message.error(data.msg)
              }
            })
          }
        })
      }
    }
  }
</script>
