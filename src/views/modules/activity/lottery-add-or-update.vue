<template>
  <div>
    <el-dialog
      :title="!dataForm.id ? '新增' : '修改'"
      :close-on-click-modal="false"
      :visible.sync="visible">
      <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()"
               label-width="120px">
        <el-form-item label="活动" prop="activityId">
          <template>
            <!--此处的回显有一个坑，dataForm.activityId应该使用字符串才可以回显-->
            <el-select v-model="dataForm.activityId" placeholder="请选择">
              <el-option
                v-for="item in activityData.list"
                :key="item.id"
                :label="item.title"
                :value="item.id">
              </el-option>
            </el-select>
          </template>
        </el-form-item>
        <el-form-item label="抽奖活动名称" prop="name">
          <el-input v-model="dataForm.name" placeholer=""></el-input>
        </el-form-item>
        <el-form-item label="活动说明" prop="notice">
          <el-input v-model="dataForm.notice" placeholer=""></el-input>
        </el-form-item>
        <el-form-item label="中奖概率" prop="probability">
          <el-input type="number" v-model="dataForm.probability" placeholer=""></el-input>
        </el-form-item>
        <el-form-item label="未中奖说明" prop="failed">
          <el-input type="textarea" v-model="dataForm.failed" placeholer=""></el-input>
        </el-form-item>
        <el-form-item label="开始日期" prop="startTime">
          <el-date-picker
            v-model="dataForm.startTime"
            type="datetime"
            format="yyyy-MM-dd HH:mm:ss"
            value-format="yyyy-MM-dd HH:mm:ss"
            placeholder="选择开始日期">
          </el-date-picker>
        </el-form-item>
        <el-form-item label="结束日期" prop="endTime">
          <el-date-picker
            v-model="dataForm.endTime"
            type="datetime"
            format="yyyy-MM-dd HH:mm:ss"
            value-format="yyyy-MM-dd HH:mm:ss"
            placeholder="选择结束日期">
          </el-date-picker>
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
        activityData: {},
        prizeIndex: 1,
        visible: false,
        typeList: [{
          value: '1',
          label: '红包'
        }, {
          value: '2',
          label: '谢谢参与'
        }],
        dataForm: {
          id: '',
          activityId: '',
          name: '',
          startTime: '',
          endTime: '',
          notice: '',
          probability: 80,
          failed: '',
          createTime: '',
          modifyTime: ''
          // prizeList: [
          //   {
          //     prizeIndex: this.prizeIndex,
          //     prizeType: '',
          //     name: '',
          //     number: '',
          //     maxPrice: '',
          //     minPrice: '',
          //   }
          // ]
        },
        dataRule: {
          activityId: [
            {required: true, message: '活动Id不能为空', trigger: 'blur'}
          ],
          name: [
            {required: true, message: '名称不能为空', trigger: 'blur'}
          ],
          startTime: [
            {required: true, message: '开始时间不能为空', trigger: 'blur'}
          ],
          endTime: [
            {required: true, message: '结束时间不能为空', trigger: 'blur'}
          ],
          notice: [
            {required: true, message: '活动说明不能为空', trigger: 'blur'}
          ],
          probability: [
            {required: true, message: '中奖概率不能为空', trigger: 'blur'}
          ],
          failed: [
            {required: true, message: '未中奖说明不能为空', trigger: 'blur'}
          ],
          type: [
            {required: true, messsage: '不能为空', trigger: 'blur'}
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
        this.getActivityData()
        this.dataForm.id = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/wheel/get/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 200) {
                this.dataForm = data.data
                this.dataForm.activityId = data.data.activityId.toString()
                for (let i = 0; i < this.dataForm.prizeList.length; i++) {
                  this.dataForm.prizeList[i].type = this.dataForm.prizeList[i].type.toString()
                }
              }
            })
          }
        })
      },
      // 获取活动数据
      getActivityData() {
        this.$http({
          url: this.$http.adornUrl('/activity/all'),
          method: 'get',
          params: this.$http.adornParams()
        }).then(({data}) => {
          if (data && data.code === 200) {
            for (let i = 0; i < data.data.length; i++) {
              // 建立索引（key value对应起来存在一个一个对象里面，在需要使用的时候直接调用）
              this.activityData[data.data[i].id] = data.data[i].title
            }
            // 选择框的数据
            this.activityData['list'] = data.data
          } else {
            this.activityData = []
          }
        })
      },
      // 表单提交
      dataFormSubmit() {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/wheel/${!this.dataForm.id ? 'create' : 'modify'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'name': this.dataForm.name,
                'activityId': this.dataForm.activityId,
                'startTime': this.dataForm.startTime,
                'endTime': this.dataForm.endTime,
                'notice': this.dataForm.notice,
                'probability': this.dataForm.probability,
                'failed': this.dataForm.failed,
                'prizeList': this.dataForm.prizeList
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
<style>
  .hr-color {
    color: lightblue;
  }
</style>
