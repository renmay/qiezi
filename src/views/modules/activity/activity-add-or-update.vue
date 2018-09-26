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

        <el-form-item label="图片url" prop="image">
          <el-input v-model="dataForm.image" placeholder=""></el-input>
        </el-form-item>

        <el-form-item label="活动图片" prop="image">
          <el-date-picker
            v-model="dataForm.startTime"
            type="datetime"
            format="yyyy-MM-dd HH:mm:ss"
            value-format="yyyy-MM-dd HH:mm:ss"
            placeholder="选择开始日期">
          </el-date-picker>
        </el-form-item>

        <el-form-item label="结束日期" prop="image">
          <el-date-picker
            v-model="dataForm.endTime"
            type="datetime"
            format="yyyy-MM-dd HH:mm:ss"
            value-format="yyyy-MM-dd HH:mm:ss"
            placeholder="选择结束日期">
          </el-date-picker>
        </el-form-item>

        <el-form-item label="活动介绍" prop="intro">
          <el-input type="testarea" v-model="dataForm.intro" placeholder=""></el-input>
        </el-form-item>
        <el-form-item label="报名须知" prop="notice">
          <el-input type="textarea" v-model="dataForm.notice" placeholder=""></el-input>
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
          createTime: '',
          endTime: '',
          image: '',
          intro: '',
          notice: '',
          season: '',
          startTime: '',
          status: '',
          title: '',
          date1: '',
          date2: ''
        },
        // 快捷选择日期
        pickerShortcut: {
          shortcuts: [{
            text: '最近一周',
            onClick (picker) {
              const end = new Date()
              const start = new Date()
              start.setTime(start.getTime() - 3600 * 1000 * 24 * 7)
              picker.$emit('pick', [start, end])
            }
          }, {
            text: '最近一个月',
            onClick (picker) {
              const end = new Date()
              const start = new Date()
              start.setTime(start.getTime() - 3600 * 1000 * 24 * 30)
              picker.$emit('pick', [start, end])
            }
          }, {
            text: '最近三个月',
            onClick (picker) {
              const end = new Date()
              const start = new Date()
              start.setTime(start.getTime() - 3600 * 1000 * 24 * 90)
              picker.$emit('pick', [start, end])
            }
          }]
        },
        dataRule: {
          endTime: [
            {required: true, message: '不能为空', trigger: 'blur'}
          ],
          intro: [
            {required: true, message: '不能为空', trigger: 'blur'}
          ],
          notice: [
            {required: true, message: '不能为空', trigger: 'blur'}
          ],
          startTime: [
            {required: true, message: '不能为空', trigger: 'blur'}
          ],
          title: [
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
      init (id) {
        this.dataForm.id = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/activity?id=${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 200) {
                console.log(data)
                this.dataForm.title = data.data.title
                this.dataForm.image = data.data.image
                this.dataForm.intro = data.data.intro
                this.dataForm.notice = data.data.notice
                this.dataForm.season = data.data.season
                this.dataForm.status = data.data.status
                this.dataForm.startTime = data.data.startTime
                this.dataForm.endTime = data.data.endTime
              }
            })
          }
        })
      },
      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/activity/${!this.dataForm.id ? 'add' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'title': this.dataForm.title,
                'startTime': this.dataForm.startTime,
                'endTime': this.dataForm.endTime,
                'image': this.dataForm.image,
                'intro': this.dataForm.intro,
                'notice': this.dataForm.notice
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
