<template>
  <div>
    <el-dialog
      :title="!dataForm.id ? '新增' : '修改'"
      :close-on-click-modal="false"
      :visible.sync="visible">
      <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()"
               label-width="80px">
        <el-form-item label="用户名" prop="username">
          <el-input v-model="dataForm.username" placeholder=""></el-input>
        </el-form-item>
        <el-form-item label="店铺名" prop="storeName" v-if="!dataForm.id">
          <el-input v-model="dataForm.storeName" placeholder=""></el-input>
        </el-form-item>
        <el-form-item label="昵称" prop="linkName">
          <el-input v-model="dataForm.linkName" placeholder=""></el-input>
        </el-form-item>
        <el-form-item label="性别" size="mini" prop="sex">
          <el-radio-group v-model="dataForm.sex">
            <el-radio :label="0">男</el-radio>
            <el-radio :label="1">女</el-radio>
          </el-radio-group>
        </el-form-item>
        <el-form-item label="电话" prop="tel">
          <el-input v-model="dataForm.tel" placeholder=""></el-input>
        </el-form-item>
        <el-form-item label="手机" prop="mobile">
          <el-input v-model="dataForm.mobile" placeholder="" type="number" max="6"></el-input>
        </el-form-item>
        <el-form-item label="地址" prop="address">
          <el-input v-model="dataForm.address" placeholder=""></el-input>
        </el-form-item>
        <el-form-item label="电子邮箱" prop="email">
          <el-input v-model="dataForm.email" placeholder=""></el-input>
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
  import { isMobile, isEmail } from '@/utils/validate'

  export default {
    data: function () {
      var validateEmail = (rule, value, callback) => {
        if (!isEmail(value)) {
          callback(new Error('邮箱格式错误'))
        } else {
          callback()
        }
      }
      var validateMobile = (rule, value, callback) => {
        if (!isMobile(value)) {
          callback(new Error('手机号格式错误'))
        } else {
          callback()
        }
      }
      return {
        value: '',
        visible: false,
        dataForm: {
          id: 0,
          linkName: '',
          tel: '',
          mobile: '',
          address: '',
          email: '',
          sex: '',
          username: ''
        },
        dataRule: {
          linkName: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          tel: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          mobile: [
            { validator: validateMobile, trigger: 'blur' }
          ],
          address: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          email: [
            { validator: validateEmail, trigger: 'blur' }
          ],
          sex: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          username: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    components: {
      ImageUpload
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
              url: this.$http.adornUrl(`/merchant?id=${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 200) {
                this.dataForm.linkName = data.data.linkName
                this.dataForm.tel = data.data.tel
                this.dataForm.mobile = data.data.mobile
                this.dataForm.address = data.data.address
                this.dataForm.email = data.data.email
                this.dataForm.sex = data.data.sex
                this.dataForm.username = data.data.username
                this.dataForm.storeName = data.data.storeName
              }
            })
          }
        })
      },
      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          console.log(this.dataForm.startTime)
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/merchant/${!this.dataForm.id ? 'add' : 'edit'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'linkName': this.dataForm.linkName,
                'tel': this.dataForm.tel,
                'mobile': this.dataForm.mobile,
                'address': this.dataForm.address,
                'email': this.dataForm.email,
                'sex': this.dataForm.sex,
                'username': this.dataForm.username,
                'storeName': this.dataForm.storeName
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
      }
    }
  }
</script>
