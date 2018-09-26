<template>
  <div>
    <el-dialog
      :title="!dataForm.id ? '新增' : '修改'"
      :close-on-click-modal="false"
      :visible.sync="visible">
      <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()"
               label-width="120px">
        <el-form-item label="抽奖活动" prop="typeId">
          <el-select v-model="dataForm.typeId" clearable placeholder="请选择活动">
            <el-option
              v-for="item in lotteryData.list"
              :key="item.id"
              :label="item.name"
              :value="item.id">
            </el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="抽奖活动类型" size="mini" prop="type">
          <el-radio-group v-model="dataForm.type">
            <el-radio :label="1">活动类型</el-radio>
            <el-radio :label="2">活动类型</el-radio>
          </el-radio-group>
        </el-form-item>
        <el-form-item label="奖品名称" prop="name">
          <el-input v-model="dataForm.name" placeholder=""></el-input>
        </el-form-item>
        <el-form-item label="奖品数量" prop="number">
          <el-input type="number" v-model="dataForm.number" placeholder=""></el-input>
        </el-form-item>
        <el-form-item label="奖品图片" prop="url">
          <el-input v-model="dataForm.url" placeholder=""></el-input>
        </el-form-item>
        <el-form-item label="奖品类型" size="mini" prop="prizeType">
          <el-radio-group v-model="dataForm.prizeType">
            <el-radio :label="1">红包</el-radio>
            <el-radio :label="2">其他</el-radio>
          </el-radio-group>
        </el-form-item>
        <el-form-item label="红包最大金额" prop="maxPrice" v-if="dataForm.prizeType == 1">
          <el-input type="number" v-model="dataForm.maxPrice" placeholder=""></el-input>
        </el-form-item>
        <el-form-item label="红包最小金额" prop="minPrice" v-if="dataForm.prizeType == 1">
          <el-input type="number" v-model="dataForm.minPrice" placeholder=""></el-input>
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
        prizeIndex: 2,
        value: '',
        visible: false,
        lotteryData: {},
        dataForm: {
          id: '',
          prizeType: '',
          name: '',
          number: '',
          maxPrice: '',
          minPrice: '',
          typeId: '',
          type: '',
          url: ''
        },
        dataRule: {
          prizeType: [
            {required: true, message: '不能为空', trigger: 'blur'}
          ],
          name: [
            {required: true, message: '不能为空', trigger: 'blur'}
          ],
          number: [
            {required: true, message: '不能为空', trigger: 'blur'}
          ],
          maxPrice: [
            {required: true, message: '不能为空', trigger: 'blur'}
          ],
          minPrice: [
            {required: true, message: '不能为空', trigger: 'blur'}
          ],
          type: [
            {required: true, message: '不能为空', trigger: 'blur'}
          ],
          typeId: [
            {required: true, message: '不能为空', trigger: 'blur'}
          ],
          url: [
            {required: true, message: '不能为空', trigger: 'blur'}
          ]
        }
      }
    },
    components: {
      ImageUpload
    },
    created() {
      this.getLotteryData()
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
              url: this.$http.adornUrl(`/prize/get/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 200) {
                this.dataForm.id = data.data.id
                this.dataForm.typeId = parseInt(data.data.typeId)
                this.dataForm.type = data.data.type
                this.dataForm.prizeType = data.data.prizeType
                this.dataForm.name = data.data.name
                this.dataForm.number = data.data.number
                this.dataForm.maxPrice = data.data.maxPrice
                this.dataForm.minPrice = data.data.minPrice
                this.dataForm.status = data.data.status
                this.dataForm.url = data.data.url
              }
            })
          }
        })
      },
      // 获取活动数据(筛选活动报名人数)
      getLotteryData() {
        this.$http({
          url: this.$http.adornUrl('/wheel/all'),
          method: 'get',
          params: this.$http.adornParams()
        }).then(({data}) => {
          if (data && data.code === 200) {
            for (let i = 0; i < data.data.length; i++) {
              this.lotteryData[data.data[i].id] = data.data[i].name
            }
            // 选择框的数据
            this.lotteryData['list'] = data.data
            console.log(this.lotteryData)
          } else {
            this.lotteryData = []
          }
        })
      },
      // 表单提交
      dataFormSubmit() {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/prize/${!this.dataForm.id ? 'create' : 'modify'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'type': this.dataForm.type,
                'typeId': this.dataForm.typeId,
                'prizeType': this.dataForm.prizeType,
                'name': this.dataForm.name,
                'number': this.dataForm.number,
                'maxPrice': this.dataForm.maxPrice,
                'minPrice': this.dataForm.minPrice,
                'url': this.dataForm.url,
                'status': this.dataForm.status
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
