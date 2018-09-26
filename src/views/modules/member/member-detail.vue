<template>
  <el-dialog
    :title="!dataForm.id ? '无数据' : '用户详情'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
      <el-form-item label="头像" prop="avatar">
        <el-input v-model="dataForm.avatar" placeholder="" disabled></el-input>
      </el-form-item>
    <el-form-item label="姓名" prop="name">
      <el-input v-model="dataForm.name" placeholder="" disabled></el-input>
    </el-form-item>
      <el-form-item label="用户姓名" prop="username">
        <el-input v-model="dataForm.username" placeholder="" disabled></el-input>
      </el-form-item>
    <el-form-item label="昵称" prop="nickname">
      <el-input v-model="dataForm.nickname" placeholder="" disabled></el-input>
    </el-form-item>
      <el-form-item label="性别" size="mini" prop="sex">
        <el-radio-group v-model="dataForm.sex">
          <el-radio :label="1" disabled>男</el-radio>
          <el-radio :label="0" disabled>女</el-radio>
        </el-radio-group>
      </el-form-item>
      <el-form-item label="签名" prop="sign">
        <el-input v-model="dataForm.sign" placeholder="" disabled></el-input>
      </el-form-item>
      <el-form-item label="联系电话" prop="mobile">
        <el-input v-model="dataForm.mobile" placeholder="" disabled></el-input>
      </el-form-item>
      <el-form-item label="地区" prop="area">
        <el-input v-model="dataForm.area" placeholder="" disabled></el-input>
      </el-form-item>
      <el-form-item label="密码" prop="password">
        <el-input v-model="dataForm.password" placeholder="" disabled></el-input>
      </el-form-item>
      <el-form-item label="状态" size="mini" prop="status">
        <el-radio-group v-model="dataForm.status">
          <el-radio :label="2" disabled>已禁用</el-radio>
          <el-radio :label="1" disabled>正常</el-radio>
        </el-radio-group>
      </el-form-item>
    <el-form-item label="类型" prop="type">
      <el-input v-model="dataForm.type" placeholder="" disabled></el-input>
    </el-form-item>
      <el-form-item label="创建时间" prop="createTime">
        <el-input v-model="dataForm.createTime" placeholder="" disabled></el-input>
      </el-form-item>
      <el-form-item label="登陆时间" prop="lastLoginTime">
        <el-input v-model="dataForm.lastLoginTime" placeholder="" disabled></el-input>
      </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button type="primary" @click="visible = false">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
  export default {
    data () {
      return {
        visible: false,
        dataForm: {
          id: 0,
          area: '',
          avatar: '',
          createTime: '',
          lastLoginTime: '',
          mobile: '',
          name: '',
          nickname: '',
          password: '',
          sex: '',
          sign: '',
          status: '',
          type: '',
          updateTime: '',
          username: ''
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.id = id
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/member?id=${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 200) {
                this.dataForm.area = data.data.area
                this.dataForm.avatar = data.data.avatar
                this.dataForm.lastLoginTime = data.data.lastLoginTime
                this.dataForm.mobile = data.data.mobile
                this.dataForm.name = data.data.name
                this.dataForm.nickname = data.data.nickname
                this.dataForm.password = data.data.password
                this.dataForm.sex = data.data.sex
                this.dataForm.sign = data.data.sign
                this.dataForm.status = data.data.status
                this.dataForm.type = data.data.type
                this.dataForm.username = data.data.username
              }
            })
          }
        })
      }
    }
  }
</script>
