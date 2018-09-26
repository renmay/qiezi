<template>
  <div>
    <el-dialog
      :title="!dataForm.id ? '新增' : '修改'"
      :close-on-click-modal="false"
      width="70%"
      :visible.sync="visible">
      <el-steps :active="active">
        <el-step title="步骤 1">
        </el-step>
        <el-step title="步骤 2">
        </el-step>
      </el-steps>
      <div class="content-container">
        <!--步骤一：填写抽奖活动内容-->
        <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()"
                 label-width="120px" style="margin-top: 20px;">
          <div v-if="active === 0">
            <el-form-item label="抽奖活动名称" prop="name">
              <el-input v-model="dataForm.name" placeholer=""></el-input>
            </el-form-item>
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
          </div>
          <!--步骤二：填写奖品-->
          <div class="prize-container" v-if="active === 1">
            <div style="margin-bottom: 20px;">
              <el-button @click="addTab(editableTabsValue)" type="primary">添加奖品</el-button>
            </div>
            <el-tabs v-model="editableTabsValue" type="card" closable @tab-remove="removeTab">
              <el-tab-pane
                v-for="(item, index) in dataForm.prizeList"
                :key="item.title"
                :label="item.title"
                :name="item.title">
                <el-form label-width="100px">
                  <el-form-item label="奖品名称">
                    <el-input v-model="item.name" placeholder="名称"></el-input>
                  </el-form-item>
                  <el-form-item label="奖品类型">
                    <template>
                      <el-radio v-model="item.prizeType" label="1">红包</el-radio>
                      <el-radio v-model="item.prizeType" label="2">门票</el-radio>
                    </template>
                  </el-form-item>
                  <el-form-item label="红包最大金额" v-if="item.prizeType ==='1'">
                    <el-input type="number" v-model="item.maxPrice" placeholder="红包最大金额"></el-input>
                  </el-form-item>
                  <el-form-item label="红包最小金额" v-if="item.prizeType ==='1'">
                    <el-input type="number" v-model="item.minPrice" placeholder="红包最小金额"></el-input>
                  </el-form-item>
                  <el-form-item label="奖品图片">
                    <el-input v-model="item.url" placeholder="奖品图片"></el-input>
                  </el-form-item>
                  <el-form-item label="奖品数量">
                    <el-input type="number" v-model="item.number" placeholder="奖品数量"></el-input>
                  </el-form-item>
                </el-form>
              </el-tab-pane>
            </el-tabs>
          </div>
        </el-form>
      </div>
      <span slot="footer" class="dialog-footer">
        <el-button v-if="active === 0" @click="next">下一步</el-button>
        <div v-if="active === 1">
           <el-button @click="pre">上一步</el-button>
          <el-button @click="visible = false">取消</el-button>
          <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
        </div>
      </span>
    </el-dialog>
  </div>
</template>

<script>
  import ImageUpload from '../component/image-upload'

  export default {
    data: function () {
      return {
        editableTabsValue: '',
        tabIndex: 1,
        active: 0,
        activityData: {},
        tebIndex: 1,
        visible: false,
        dataForm: {
          id: '',
          activityId: '',
          name: '',
          startTime: '',
          endTime: '',
          notice: '',
          probability: 100,
          failed: '',
          createTime: '',
          modifyTime: '',
          prizeList: [{
            title: '',
            nameIndex: '1',
            prizeType: '',
            name: '奖品 1',
            number: '',
            maxPrice: '',
            minPrice: '',
          }]
        },
        dataRule: {
          name: [
            {required: true, message: '名称不能为空', trigger: 'blur'}
          ],
          activityId: [
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
          prizeType: [
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
                const arr = []
                for (let i = 0; i < this.dataForm.prizeList.length; i++) {
                  let tempData = this.dataForm.prizeList[i]
                  if (tempData.prizeType !== null) tempData.prizeType = tempData.prizeType.toString()
                  tempData.title = this.dataForm.prizeList[i].name
                  arr.push(tempData)
                }
                this.dataForm.prizeList = arr
                this.editableTabsValue = this.dataForm.prizeList[0].name

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
      // 上一步
      pre() {
        this.active--
      },
      // 下一步
      next() {
        // this.$refs['dataForm'].validate((valid) => {
        //   if (valid && this.active++ > 2) {
        //     this.active = 0
        //   }
        // })
        if(this.active++ > 2)  this.active = 0
      },
      // 添加
      addTab(targetNameIndex) {
        let newTabName = '奖品'
        this.dataForm.prizeList.push({
          name: newTabName,
          title: newTabName,
          number: '',
          maxPrice: '',
          minPrice: '',
          url: ''
        });
        this.editableTabsValue = newTabName
        console.log(this.editableTabsValue)
      },
      // 移除
      removeTab(targetName) {
        let tabs = this.dataForm.prizeList
        let activeName = this.editableTabsValue
        if (activeName === targetName) {
          tabs.forEach((tab, index) => {
            if (tab.name === targetName) {
              let nextTab = tabs[index + 1] || tabs[index - 1]
              if (nextTab) {
                activeName = nextTab.name
              }
            }
          })
        }
        this.editableTabsValue = activeName
        this.dataForm.prizeList = tabs.filter(tab => tab.name !== targetName)
      },

      // 验证奖品表单
      validatePrize() {
        for (var i = 0; i < this.dataForm.prizeList.length; i++) {
          if (this.dataForm.prizeList[i].prizeType === '' || this.dataForm.prizeList[i].prizeType === undefined || this.dataForm.prizeList[i].name === undefined) {
            return false
          }
          return true
        }
      },
      // 表单提交
      dataFormSubmit() {
        if (!this.validatePrize()) {
          this.$message.error('奖品名称和奖品类型不能为空')
          return
        }
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/wheel/${!this.dataForm.id ? 'create' : 'modify'}`),
              method: 'post',
              data: this.$http.adornData(this.dataForm)
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
  .prize-container {
    padding: 20px;
  }

  .add-prize {
    padding: 20px;
  }
</style>
