<template>
  <div class="mod-record">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item>
        <el-input v-model="dataForm.memberId" placeholder="用户Id" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <el-input v-model="dataForm.signUpId" placeholder="选手参赛Id" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <el-select v-model="dataForm.activityId" clearable placeholder="请选择活动">
          <el-option
            v-for="item in activityData.list"
            :key="item.id"
            :label="item.title"
            :value="item.id">
          </el-option>
        </el-select>
      </el-form-item>
      <el-form-item>
        <el-button @click="getDataList()">查询</el-button>
        <el-button v-if="dataForm.activityId || dataForm.signUpId ||dataForm.memberId" @click="clearDataForm()">重置
        </el-button>
      </el-form-item>
    </el-form>
    <el-table
      :data="dataList"
      border
      v-loading="dataListLoading"
      @selection-change="selectionChangeHandle"
      style="width: 100%;">
      <el-table-column prop="id" header-align="center" align="center" label="id">
      </el-table-column>
      <el-table-column prop="memberId" header-align="center" align="center" label="用户id">
      </el-table-column>
      <el-table-column prop="activityId" header-align="center" align="center" label="活动名称">
      </el-table-column>
      <el-table-column prop="signUpId" header-align="center" align="center" label="报名id">
      </el-table-column>
      <el-table-column prop="votes" header-align="center" align="center" label="票数">
      </el-table-column>
      <el-table-column prop="createTime" header-align="center" align="center" label="创建时间">
      </el-table-column>
      <el-table-column prop="status" header-align="center" align="center" label="状态">
      </el-table-column>
    </el-table>
    <el-pagination
      @size-change="sizeChangeHandle"
      @current-change="currentChangeHandle"
      :current-page="pageIndex"
      :page-sizes="[10, 20, 50, 100]"
      :page-size="pageSize"
      :total="totalPage"
      layout="total, sizes, prev, pager, next, jumper">
    </el-pagination>
  </div>
</template>

<script>

  export default {
    data() {
      return {
        dataForm: {
          memberId: '',
          activityId: '',
          signUpId: ''
        },
        activityData: {},
        dataList: [],
        pageIndex: 1,
        pageSize: 10,
        totalPage: 0,
        dataListLoading: false,
        dataListSelections: [],
        addOrUpdateVisible: false
      }
    },
    created(){
      this.getActivityData()
    },
    activated() {
      this.getDataList()
      this.getActivityData()
    },
    methods: {
      // 重置
      clearDataForm() {
        this.dataForm = {}
        this.getDataList()
      },
      // 获取数据列表
      getDataList() {
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/vote/record/list'),
          method: 'get',
          params: this.$http.adornParams({
            'pageNo': this.pageIndex,
            'pageSize': this.pageSize,
            'memberId': this.dataForm.memberId,
            'signUpId': this.dataForm.signUpId,
            'activityId': this.dataForm.activityId
          })
        }).then(({data}) => {
          if (data && data.code === 200) {
            this.dataList = data.data.list
            for (let i = 0; i < this.dataList.length; i++) {
              this.dataList[i].activityId = this.activityData[this.dataList[i].activityId]
            }
            this.totalPage = data.data.totalCount
          } else {
            this.dataList = []
            this.totalPage = 0
          }
          this.dataListLoading = false
        })
      },
      // 获取活动数据(筛选活动报名人数)
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
      // 每页数
      sizeChangeHandle(val) {
        this.pageSize = val
        this.pageIndex = 1
        this.getDataList()
      },
      // 当前页
      currentChangeHandle(val) {
        this.pageIndex = val
        this.getDataList()
      },
      // 多选
      selectionChangeHandle(val) {
        this.dataListSelections = val
      },
      // 删除
      deleteHandle(id) {
        var ids = id ? [id] : this.dataListSelections.map(item => {
          return item.id
        })
        this.$confirm(`确定对[id=${ids.join(',')}]进行[${id ? '删除' : '批量删除'}]操作?`, '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$http({
            url: this.$http.adornUrl('/vote/record/delete'),
            method: 'post',
            data: ids
          }).then(({data}) => {
            if (data && data.code === 200) {
              this.$message({
                message: '操作成功',
                type: 'success',
                duration: 1500,
                onClose: () => {
                  this.getDataList()
                }
              })
            } else {
              this.$message.error(data.msg)
            }
          })
        })
      }
    }
  }
</script>
