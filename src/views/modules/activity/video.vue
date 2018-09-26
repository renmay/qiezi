<template>
  <div class="mod-config">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
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
        <el-select v-model="dataForm.type" clearable placeholder="请选择视频类型">
          <el-option
            v-for="item in typeData"
            :key="item.type"
            :label="item.label"
            :value="item.type">
          </el-option>
        </el-select>
      </el-form-item>
      <el-form-item>
        <el-input v-model="dataForm.title" placeholder="视频标题" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <el-button @click="getDataList()">查询</el-button>
        <el-button v-if="dataForm.title || dataForm.type" @click="clearDataForm()">重置</el-button>
        <el-button v-if="isAuth('activity:video:delete')" type="danger" @click="deleteHandle()" :disabled="dataListSelections.length <= 0">批量删除</el-button>
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
        prop="videoId"
        header-align="center"
        align="center"
        label="videoId">
      </el-table-column>
      <el-table-column
        prop="title"
        header-align="center"
        align="center"
        label="视频标题">
      </el-table-column>
      <el-table-column
        width="180"
        prop="url"
        header-align="center"
        align="center"
        label="视频url">
        <template slot-scope="scope">
          <router-link to="/scope.row.url">
           <el-tag @click="openUrl(scope.row.url)">{{scope.row.url}}</el-tag>
          </router-link>
        </template>
      </el-table-column>
      <el-table-column
        prop="activityId"
        header-align="center"
        align="center"
        label="活动名称">
      </el-table-column>
      <el-table-column
        prop="type"
        header-align="center"
        align="center"
        label="类型">
        <template slot-scope="scope">
          <el-tag v-if="scope.row.type === 1" size="small" type="success">个人</el-tag>
          <el-tag v-else size="small">战队</el-tag>
        </template>
      </el-table-column>
      <el-table-column
        width="200"
        prop="createTime"
        header-align="center"
        align="center"
        label="上传时间">
      </el-table-column>
      <el-table-column
        fixed="right"
        header-align="center"
        align="center"
        width="150"
        label="操作">
        <template slot-scope="scope">
          <el-button type="primary" size="mini" @click="addOrUpdateHandle(scope.row.id)">修改</el-button>
          <el-button type="primary" size="mini" @click="deleteHandle(scope.row.id)">删除</el-button>
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
  import AddOrUpdate from './video-add-or-update'
  export default {
    data () {
      return {
        dataForm: {
          activityId: '',
          type: '',
          title: ''
        },
        dataList: [],
        activityData: [],
        typeData: [{
          type: '1',
          label: '个人'
        }, {
          type: '2',
          label: '战队'
        }],
        pageIndex: 1,
        pageSize: 10,
        totalPage: 0,
        dataListLoading: false,
        addOrUpdateVisible: false,
        dataListSelections: []
      }
    },
    components: {
      AddOrUpdate
    },
    activated () {
      this.getActivityData()
      this.getDataList()
    },
    methods: {
      // 打开视频链接
      openUrl (url) {
        window.location.href = url
      },
      // 重置
      clearDataForm () {
        this.dataForm = {}
        this.getDataList()
      },
      // 获取数据列表
      getDataList () {
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/video/list'),
          method: 'get',
          params: this.$http.adornParams({
            'pageNo': this.pageIndex,
            'pageSize': this.pageSize,
            'activityId': this.dataForm.activityId,
            'type': this.dataForm.type,
            'title': this.dataForm.title
          })
        }).then(({data}) => {
          if (data && data.code === 200) {
            this.dataList = data.data.list
            for (let i = 0; i < this.dataList.length; i++) {
              // 此时就在使用"索引"方法，使得列表中的id变成下边写好的title值（当然可以是其他值）
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
      // 获取活动数据列表(筛选活动视频)
      getActivityData () {
        this.$http({
          url: this.$http.adornUrl('/activity/all'),
          method: 'get',
          params: this.$http.adornParams()
        }).then(({data}) => {
          if (data && data.code === 200) {
            // 选择框的数据
            this.activityData['list'] = data.data
            // 循环 建立索引
            for (let i = 0; i < data.data.length; i++) {
              this.activityData[data.data[i].id] = data.data[i].title
            }
          } else {
            this.activityData = []
          }
        })
      },
      // 每页数
      sizeChangeHandle (val) {
        this.pageSize = val
        this.pageIndex = 1
        this.getDataList()
      },
      // 当前页
      currentChangeHandle (val) {
        this.pageIndex = val
        this.getDataList()
      },
      // 多选
      selectionChangeHandle (val) {
        this.dataListSelections = val
      },
      // 新增 / 修改
      addOrUpdateHandle (id) {
        this.addOrUpdateVisible = true
        this.$nextTick(() => {
          this.$refs.addOrUpdate.init(id)
        })
      },
      // 删除
      deleteHandle (id) {
        var ids = id ? [id] : this.dataListSelections.map(item => {
          return item.id
        })
        this.$confirm(`确定对[id=${ids.join(',')}]进行[${id ? '禁用' : '批量禁用'}]操作?`, '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$http({
            url: this.$http.adornUrl('/video/delete'),
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
