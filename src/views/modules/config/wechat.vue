<template>
  <div>
    <el-form :model="dataForm" ref="dataForm"
             label-width="100px">
      <el-form-item label="key" prop="key">
        <el-input v-model="dataForm.key" placeholder="" disabled></el-input>
      </el-form-item>
      <el-form-item label="aesKey" prop="aesKey">
        <el-input v-model="dataForm.aesKey" placeholder="" disabled></el-input>
      </el-form-item>
      <el-form-item label="secret" prop="secret">
        <el-input v-model="dataForm.secret" placeholder="" disabled></el-input>
      </el-form-item>
      <el-form-item label="appId" prop="appId">
        <el-input v-model="dataForm.appId" placeholder="" disabled></el-input>
      </el-form-item>
      <el-form-item label="token" prop="token">
        <el-input v-model="dataForm.token" placeholder="" disabled></el-input>
      </el-form-item>
      <el-form-item label="商户号" prop="mchId" disabled>
        <el-input v-model="dataForm.mchId" placeholder="" disabled></el-input>
      </el-form-item>
      <el-form-item label="证书路径" prop="certPath">
        <el-input v-model="dataForm.certPath" placeholder="" disabled></el-input>
      </el-form-item>
      <el-form-item label="noticeUrl" prop="noticeUrl">
        <el-input v-model="dataForm.noticeUrl" placeholder="" disabled></el-input>
      </el-form-item>
      <el-form-item label="子商户appid" prop="subAppId">
        <el-input v-model="dataForm.subAppId" placeholder="" disabled></el-input>
      </el-form-item>
      <el-form-item label="子商户商户号" prop="subMchId">
        <el-input v-model="dataForm.subMchId" placeholder="" disabled></el-input>
      </el-form-item>
    </el-form>
    <el-row :gutter="20">
      <el-col :span="6" :offset="20"><div><el-button type="primary" @click="addOrUpdateHandle">修改</el-button></div></el-col>
    </el-row>
    <!-- 弹窗-->
    <add-or-update v-if="addOrUpdateVisible" ref="addOrUpdate"></add-or-update>
  </div>
</template>

<script>
  import AddOrUpdate from './wechat-add-or-update'

  export default {
    data () {
      return {
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
        addOrUpdateVisible: false
      }
    },
    components: {
      AddOrUpdate
    },
    created () {
      this.init()
    },
    methods: {
      // 获取表单数据
      init () {
        this.$http({
          url: this.$http.adornUrl(`/third/account/config/weixin`),
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
      // 修改
      addOrUpdateHandle () {
        this.addOrUpdateVisible = true
        this.$nextTick(() => {
          this.$refs.addOrUpdate.init()
        })
      }
    }
  }
</script>
<style>
  input:disabled {
    color: #444 !important;
  }
</style>
