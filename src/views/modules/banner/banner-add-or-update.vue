<template>
  <div>
    <el-dialog
      :title="!dataForm.id ? '新增' : '修改'"
      :close-on-click-modal="false"
      :visible.sync="visible">
      <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()"
               label-width="80px">
        <el-form-item label="标题" prop="title">
          <el-input v-model="dataForm.title" placeholder=""></el-input>
        </el-form-item>
        <el-form-item label="图片" prop="image">
          <el-input v-model="dataForm.image" placeholder=""></el-input>
        </el-form-item>
        <el-form-item label="跳转url" prop="url">
          <el-input v-model="dataForm.url" placeholder="/store/123456"></el-input>
        </el-form-item>
        <el-form-item label="类型" size="mini" prop="status">
          <el-radio-group v-model="dataForm.type">
            <el-radio :label="1">首页banner</el-radio>
            <el-radio :label="2">推理社banner</el-radio>
          </el-radio-group>
        </el-form-item>
        <el-form-item label="排序" prop="sort">
          <el-input type="number" v-model="dataForm.sort" placeholder=""></el-input>
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
          id: 0,
          title: '',
          image: '',
          url: '',
          type: '',
          createTime: '',
          updateTime: '',
          status: '',
          sort: ''
        },
        dataRule: {
          title: [
            {required: true, message: '不能为空', trigger: 'blur'}
          ],
          // image: [
          //   {required: true, message: '不能为空', trigger: 'blur'}
          // ],
          url: [
            {required: true, message: '不能为空', trigger: 'blur'}
          ],
          type: [
            {required: true, message: '不能为空', trigger: 'blur'}
          ],
          sort: [
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
      init(id) {
        this.dataForm.id = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/banner/get/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 200) {
                console.log(data)
                this.dataForm.title = data.data.title
                this.dataForm.image = data.data.image
                this.dataForm.url = data.data.url
                this.dataForm.type = data.data.type
                this.dataForm.createTime = data.data.createTime
                this.dataForm.updateTime = data.data.updateTime
                this.dataForm.status = data.data.status
                this.dataForm.sort = data.data.sort
              }
            })
          }
        })
      },
      // 表单提交
      dataFormSubmit() {
        this.$refs['dataForm'].validate((valid) => {
          console.log(this.dataForm.startTime)
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/banner/${!this.dataForm.id ? 'create' : 'modify'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'title': this.dataForm.title,
                'image': this.dataForm.image,
                'url': this.dataForm.url,
                'type': this.dataForm.type,
                'sort': parseInt(this.dataForm.sort)
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
