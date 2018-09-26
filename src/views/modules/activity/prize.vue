<template>
  <div class="mod-config">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item>
        <el-select v-model="dataForm.typeId" clearable placeholder="请选择活动">
          <el-option
            v-for="item in lotteryData.list"
            :key="item.id"
            :label="item.name"
            :value="item.id">
          </el-option>
        </el-select>
      </el-form-item>
      <el-form-item>
        <el-button @click="getDataList()">查询</el-button>
        <el-button v-if="dataForm.title" @click="clearDataForm()">重置</el-button>
        <el-button v-if="isAuth('activity:prize:save')" type="primary" @click="addOrUpdateHandle()">新增</el-button>
        <el-button v-if="isAuth('activity:prize:delete')" type="danger" @click="deleteHandle()"
                   :disabled="dataListSelections.length <= 0">批量删除
        </el-button>
      </el-form-item>
    </el-form>
    <el-table
      :data="dataList"
      border
      v-loading="dataListLoading"
      @selection-change="selectionChangeHandle"
      style="width: 100%;">
      <el-table-column
        type="selection"
        header-align="center"
        align="center"
        width="50">
      </el-table-column>
      <el-table-column
        prop="id"
        header-align="center"
        align="center"
        label="id">
      </el-table-column>
      <el-table-column
        prop="typeId"
        header-align="center"
        align="center"
        label="抽奖活动id">
      </el-table-column>
      <el-table-column
        width="140"
        prop="typeName"
        header-align="center"
        align="center"
        label="抽奖活动名称">
      </el-table-column>
      <el-table-column
        prop="type"
        header-align="center"
        align="center"
        label="类型">
      </el-table-column>
      <el-table-column
        prop="prizeType"
        header-align="center"
        align="center"
        label="其他">
      </el-table-column>
      <el-table-column
        prop="name"
        header-align="center"
        align="center"
        label="名称">
      </el-table-column>
      <el-table-column
        prop="number"
        header-align="center"
        align="center"
        label="奖品数量">
      </el-table-column>
      <el-table-column
        prop="maxPrice"
        header-align="center"
        align="center"
        label="红包最大金额">
      </el-table-column>
      <el-table-column
        prop="minPrice"
        header-align="center"
        align="center"
        label="红包最小金额">
      </el-table-column>
      <el-table-column
        prop="status"
        header-align="center"
        align="center"
        label="状态">
      </el-table-column>
      <el-table-column
        fixed="right"
        header-align="center"
        align="center"
        width="150"
        label="操作">
        <template slot-scope="scope">
          <el-button type="primary" size="mini" @click="addOrUpdateHandle(scope.row.id)">修改</el-button>
          <el-button type="primary" size="mini" @click="deleteHandle(scope.row.id)">删除
          </el-button>
        </template>
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
    <!-- 弹窗, 新增 / 修改 -->
    <add-or-update v-if="addOrUpdateVisible" ref="addOrUpdate" @refreshDataList="getDataList"></add-or-update>
  </div>
</template>

<script>
  import AddOrUpdate from './prize-add-or-update'

  export default {
    data() {
      return {
        typeId: '',
        di: '',
        dataForm: {
          id: '',
          title: '',
          typeId: ''
        },
        lotteryData: {},
        dataList: [],
        pageIndex: 1,
        pageSize: 10,
        totalPage: 0,
        dataListLoading: false,
        dataListSelections: [],
        addOrUpdateVisible: false
      }
    },
    components: {
      AddOrUpdate
    },
    activated() {
      this.getDataList()
      this.getLotteryData()

    },
    created() {
      this.getDataList()
      this.getLotteryData()
    },
    // 用于不同页面穿参
    watch: {
      // 监测路由变化,只要变化了就调用获取路由参数方法将数据存储本组件即可
      '$route': 'getDataList'
    },
    methods: {
      // 重置
      clearDataForm() {
        this.dataForm = {}
        this.getDataList()
      },
      getDataList() {
        // 取到路由带过来的参数
        const routerParams = this.$route.params.id
        // 将数据放在当前组件的数据内
        this.id = routerParams
        this.dataListLoading = true
        if (this.id === undefined) {
          this.$http({
            url: this.$http.adornUrl(`/prize/list`),
            method: 'get',
            params: this.$http.adornParams({
              'pageNo': this.pageIndex,
              'pageSize': this.pageSize,
              'typeId': this.dataForm.typeId
            })
          }).then(({data}) => {
            if (data && data.code === 200) {
              this.dataList = data.data.list
            } else {
              this.dataList = []
              this.totalPage = 0
            }
            this.dataListLoading = false
          })
        } else {
          this.$http({
            url: this.$http.adornUrl(`/prize/list/${this.id}`),
            method: 'get',
            params: this.$http.adornParams()
          }).then(({data}) => {
            if (data && data.code === 200) {
              this.dataList = data.data
            } else {
              this.dataList = []
              this.totalPage = 0
            }
            this.dataListLoading = false
          })
        }
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
      // 新增 / 修改
      addOrUpdateHandle(id) {
        // // TODO 添加奖品列表
        this.addOrUpdateVisible = true
        this.$nextTick(() => {
          this.$refs.addOrUpdate.init(id)
        })
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
            url: this.$http.adornUrl('/prize/delete'),
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
