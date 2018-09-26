<template>
  <div>
    <el-dialog
      :title="!dataForm.id ? '新增' : '修改'"
      :close-on-click-modal="false"
      :visible.sync="visible">
      <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()"
               label-width="140px">
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
        <el-form-item>
          <span>* 每天允许投票次数 = 微信每天投票次数 + APP每天投票次数</span>
        </el-form-item>
        <el-form-item label="每天允许投票次数" prop="times">
          <el-input type="number" v-model="dataForm.times" placeholder=""></el-input>
        </el-form-item>
        <el-form-item label="微信每天投票次数" prop="weixinTimes">
          <el-input type="number" v-model="dataForm.weixinTimes" placeholder=""></el-input>
        </el-form-item>
        <el-form-item label="APP每天投票次数" prop="appTimes">
          <el-input type="number" v-model="dataForm.appTimes" placeholder=""></el-input>
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
        },
        dataRule: {
          activityId: [
            {required: true, message: '不能为空', trigger: 'blur'}
          ],
          times: [
            {required: true, message: '不能为空', trigger: 'blur'}
          ],
          weixinTimes: [
            {required: true, message: '不能为空', trigger: 'blur'}
          ],
          appTimes: [
            {required: true, message: '不能为空', trigger: 'blur'}
          ]
        }
      }
    },
    components: {
      ImageUpload
    },
    created() {
      this.getActivityData()
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
              url: this.$http.adornUrl(`/vote/config/get/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 200) {
                this.dataForm = data.data
                this.dataForm.activityId = data.data.activityId.toString()
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
        let times = parseInt(this.dataForm.times)
        let appTimes = parseInt(this.dataForm.appTimes)
        let weixinTimes = parseInt(this.dataForm.weixinTimes)
        if ( times === appTimes +weixinTimes) {
          this.$refs['dataForm'].validate((valid) => {
            if (valid) {
              this.$http({
                url: this.$http.adornUrl(`/vote/config/${!this.dataForm.id ? 'create' : 'modify'}`),
                method: 'post',
                data: this.$http.adornData({
                  'id': this.dataForm.id || undefined,
                  'activityId': this.dataForm.activityId,
                  'times': parseInt(this.dataForm.times),
                  'weixinTimes': parseInt(this.dataForm.weixinTimes),
                  'appTimes': parseInt(this.dataForm.appTimes)
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
        }else{
          this.$message.error("投票次数设置错误：每天允许投票次数 = 微信每天投票次数 + APP每天投票次数")
        }
      }
    }
  }
</script>
<style>
  .hr-color {
    color: lightblue;
  }
</style>
