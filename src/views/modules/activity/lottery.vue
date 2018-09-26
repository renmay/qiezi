<template>
  <div class="mod-config">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <!--<el-form-item>-->
        <!--<el-input v-model="dataForm.activityId" placeholder="活动Id" clearable></el-input>-->
      <!--</el-form-item>-->
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
        <el-button v-if="dataForm.activityId" @click="clearDataForm()">重置</el-button>
        <el-button v-if="isAuth('activity:lottery:save')" type="primary" @click="addOrUpdateHandle()">新增</el-button>
      </el-form-item>
    </el-form>
    <el-table
      :data="dataList"
      border
      v-loading="dataListLoading"
      @selection-change="selectionChangeHandle"
      style="width: 100%;">
      <!-- 抽奖活动的奖品信息-->
      <!--<el-table-column type="expand">-->
        <!--<template slot-scope="props">-->
          <!--<el-table-->
            <!--:data=props.row.prizeList-->
            <!--border-->
            <!--v-loading="dataListLoading"-->
            <!--@selection-change="selectionChangeHandle"-->
            <!--style="width: 100%;">-->
            <!--<span>{{props.row.id}}</span>-->
            <!--<el-table-column prop="props.row.id" label="抽奖活动Id" align="center"></el-table-column>-->
            <!--<el-table-column prop="name" label="奖品名称" align="center"></el-table-column>-->
            <!--<el-table-column-->
              <!--prop="type"-->
              <!--header-align="center"-->
              <!--align="center"-->
              <!--label="类型">-->
              <!--<template slot-scope="scope">-->
                <!--<el-tag v-if="scope.row.type === 1" size="small" type="success">红包</el-tag>-->
                <!--<el-tag v-else size="small" type="info">其他</el-tag>-->
              <!--</template>-->
            <!--</el-table-column>-->
            <!--<el-table-column prop="number" label="奖品数量" align="center"></el-table-column>-->
            <!--<el-table-column prop="maxPrice" label="红包最大金额" align="center"></el-table-column>-->
            <!--<el-table-column prop="minPrice" label="红包最小金额" align="center"></el-table-column>-->
          <!--</el-table>-->
        <!--</template>-->
      <!--</el-table-column>-->
      <!--<el-table-column-->
        <!--prop="id"-->
        <!--align="center"-->
        <!--label="抽奖活动id">-->
      <!--</el-table-column>-->
      <el-table-column
        prop="name"
        align="center"
        width="140"
        label="抽奖活动名称">
      </el-table-column>
      <el-table-column
        width="140"
        prop="activityId"
        align="center"
        label="活动名称">
      </el-table-column>
      <el-table-column
        prop="startTime"
        align="center"
        label="开始时间">
      </el-table-column>
      <el-table-column
        prop="endTime"
        align="center"
        label="结束时间">
      </el-table-column>
      <el-table-column
        prop="notice"
        align="center"
        label="活动说明">
      </el-table-column>
      <el-table-column
        prop="probability"
        align="center"
        label="填写80">
      </el-table-column>
      <el-table-column
        prop="failed"
        align="center"
        label="未中奖说明">
      </el-table-column>
      <el-table-column
        prop="createTime"
        align="center"
        label="创建时间">
      </el-table-column>
      <el-table-column
        prop="modifyTime"
        align="center"
        label="修改时间">
      </el-table-column>
      <el-table-column
        prop="status"
        header-align="center"
        align="center"
        label="状态">
        <template slot-scope="scope">
          <el-tag v-if="scope.row.status === 1" size="small" type="success">正常</el-tag>
          <el-tag v-else size="small" type="info">关闭</el-tag>
        </template>
      </el-table-column>
      <el-table-column
        fixed="right"
        header-align="center"
        align="center"
        width="300"
        label="操作">
        <template slot-scope="scope">
          <el-button type="primary" size="mini" @click="addOrUpdateHandle(scope.row.id)">修改</el-button>
          <el-button type="primary" size="mini" @click="openHandle(scope.row.id)" v-show="scope.row.status==0">开启
          </el-button>
          <el-button type="primary" size="mini" @click="getPrizeList(scope.row.id)">查看奖品</el-button>
          <el-button type="primary" size="mini" @click="closeHandle(scope.row.id)" v-show="scope.row.status==1">关闭
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
  import AddOrUpdate from './lottery-add-or-update'

  export default {
    data() {
      return {
        dataForm: {
          activityId: ''
        },
        prizeList: {},
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
    components: {
      AddOrUpdate
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
          url: this.$http.adornUrl('/wheel/list'),
          method: 'get',
          params: this.$http.adornParams({
            'pageNo': this.pageIndex,
            'pageSize': this.pageSize,
            'activityId': this.dataForm.activityId
          })
        }).then(({data}) => {
          if (data && data.code === 200) {
            this.dataList = data.data.list
            this.totalPage = data.data.totalCount
            for (let i = 0; i < this.dataList.length; i++) {
              this.dataList = data.data.list
              this.dataList[i].activityId = this.activityData[this.dataList[i].activityId]
            }
          } else {
            this.dataList = []
            this.totalPage = 0
          }
          this.dataListLoading = false
        })
      },
      // 获取活动数据
      getActivityData () {
        this.$http({
          url: this.$http.adornUrl('/activity/all'),
          method: 'get',
          params: this.$http.adornParams()
        }).then(({data}) => {
          if (data && data.code === 200) {
            for (let i = 0; i < data.data.length; i++) {
              this.activityData[data.data[i].id] = data.data[i].title
            }
            // 选择框的数据
            this.activityData['list'] = data.data
          } else {
            this.activityData = []
          }
        })
      },
      getPrizeList(id) {
        console.log(id)
        this.$router.push({
          path: '/activity-prize',
          name: 'activity-prize',
          params: {
            id: id
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
        this.addOrUpdateVisible = true
        this.$nextTick(() => {
          this.$refs.addOrUpdate.init(id)
        })
      },
      // 开启抽奖活动
      openHandle(id) {
        this.$confirm(`确定对[id=${id}]进行[${id ? '开启' : '批量删除'}]操作?`, '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$http({
            url: this.$http.adornUrl('/wheel/open'),
            method: 'post',
            params: this.$http.adornParams({
              'id': id
            })
          }).then(({data}) => {
            if (data && data.code === 200) {
              this.$message({
                message: '操作成功',
                type: 'success',
                duration: 1500
              })
              this.getDataList()
            } else {
              this.$message.error(data.msg)
            }
          })
        })
      },
      // 关闭抽奖活动
      closeHandle(id) {
        this.$confirm(`确定对[活动id=${id}]进行[${id ? '关闭' : '批量删除'}]操作?`, '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$http({
            url: this.$http.adornUrl('/wheel/close'),
            method: 'post',
            params: this.$http.adornParams({
              'id': id
            })
          }).then(({data}) => {
            if (data && data.code === 200) {
              this.$message({
                message: '操作成功',
                type: 'success',
                duration: 1500
              })
              this.getDataList()
            } else {
              this.$message.error(data.msg)
            }
          })
        })
      }
    }
  }
</script>
