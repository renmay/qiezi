<template>
  <div class="mod-config">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item>
        <el-input v-model="dataForm.name" placeholder="名称" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <el-input v-model="dataForm.merchantName" placeholder="商家姓名" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <el-button @click="getDataList()">查询</el-button>
        <el-button v-if="dataForm.name || dataForm.username" @click="clearDataForm()">重置</el-button>
        <!--<el-button v-if="isAuth('merchant:store:close')" type="danger" @click="closeHandle()" :disabled="dataListSelections.length <= 0">批量禁用</el-button>-->
      </el-form-item>
    </el-form>
    <el-table
      :data="dataList"
      border
      v-loading="dataListLoading"
      @selection-change="selectionChangeHandle"
      style="width: 100%;">
      <!--<el-table-column-->
        <!--type="selection"-->
        <!--header-align="center"-->
        <!--align="center"-->
        <!--width="50">-->
      <!--</el-table-column>-->
      <el-table-column label="店铺首图" width="100" header-align="center" align="center">
        <template slot-scope="scope">
          <img :src="scope.row.titleImage" width="40" height="40" class="image"/>
        </template>
      </el-table-column>
      <el-table-column
        prop="name"
        header-align="center"
        clign="center"
        label="名称">
      </el-table-column>
      <el-table-column
        prop="merchantName"
        header-align="center"
        clign="center"
        label="商家姓名">
      </el-table-column>
      <el-table-column
        prop="merchantMobile"
        header-align="center"
        clign="center"
        label="商家手机">
      </el-table-column>
      <el-table-column
        prop="intro"
        header-align="center"
        clign="center"
        label="简介">
      </el-table-column>
      <el-table-column
        prop="receptionTel"
        header-align="center"
        clign="center"
        label="前台电话">
      </el-table-column>
      <el-table-column
        prop="status"
        header-align="center"
        align="center"
        label="状态">
        <template slot-scope="scope">
          <el-tag v-if="scope.row.status === 2" size="small" type="danger">已禁用</el-tag>
          <el-tag v-else size="small">正常</el-tag>
        </template>
      </el-table-column>
      <el-table-column
        prop="open"
        header-align="center"
        clign="center"
        label="是否营业">
      </el-table-column>
      <el-table-column
        prop="sales"
        header-align="center"
        clign="center"
        label="成交量">
      </el-table-column>
      <el-table-column
        prop="clickCount"
        header-align="center"
        clign="center"
        label="访问量">
      </el-table-column>
      <el-table-column
        prop="count"
        header-align="center"
        clign="center"
        label="商品数">
      </el-table-column>
      <el-table-column
        prop="commentCount"
        header-align="center"
        clign="center"
        label="评价数量">
      </el-table-column>
      <el-table-column
        prop="grade"
        header-align="center"
        clign="center"
        label="评分">
      </el-table-column>
      <el-table-column
        prop="address"
        header-align="center"
        clign="center"
        label="详细地址">
      </el-table-column>
      <el-table-column
        prop="tradingArea"
        header-align="center"
        clign="center"
        label="商圈">
      </el-table-column>
      <el-table-column
        prop="createTime"
        header-align="center"
        clign="center"
        label="创建时间">
      </el-table-column>
      <el-table-column
        fixed="right"
        header-align="center"
        align="center"
        width="150"
        label="操作">
        <template slot-scope="scope">
          <el-button type="primary" size="mini" @click="storeDetail(scope.row.id)">查看详情</el-button>
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
    <!-- 弹窗, 查看 -->
    <store-detail v-if="storeDetailVisible" ref="storeDetail" @refreshDataList="getDataList"></store-detail>
  </div>
</template>

<script>
  import storeDetail from './store-detail'

  export default {
    data () {
      return {
        dataForm: {
          name: '',
          username: '',
          nickname: ''
        },
        dataList: [],
        pageIndex: 1,
        pageSize: 10,
        totalPage: 0,
        dataListLoading: false,
        dataListSelections: [],
        storeDetailVisible: false
      }
    },
    components: {
      storeDetail
    },
    activated () {
      this.getDataList()
    },
    methods: {
      // 重置
      clearDataForm () {
        this.dataForm = {}
        this.getDataList()
      },
      // 获取数据列表
      getDataList () {
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/store/list'),
          method: 'get',
          params: this.$http.adornParams({
            'pageNo': this.pageIndex,
            'pageSize': this.pageSize,
            'name': this.dataForm.name,
            'username': this.dataForm.username,
            'nickname': this.dataForm.nickname
          })
        }).then(({data}) => {
          if (data && data.code === 200) {
            this.dataList = data.data.list
            this.totalPage = data.data.totalCount
          } else {
            this.dataList = []
            this.totalPage = 0
          }
          this.dataListLoading = false
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
      // 查看用户详情
      storeDetail (id) {
        console.log(id)
        this.storeDetailVisible = true
        this.$nextTick(() => {
          this.$refs.storeDetail.init(id)
        })
      },
      // 激活
      activeHandle (id) {
        var ids = id ? [id] : this.dataListSelections.map(item => {
          return item.id
        })
        this.$confirm(`确定对[id=${ids.join(',')}]进行[${id ? '激活' : '批量激活'}]操作?`, '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$http({
            url: this.$http.adornUrl('/store/active'),
            method: 'post',
            params: this.$http.adornParams({
              'id': id
            })
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
      },
      // 禁用
      closeHandle (id) {
        var ids = id ? [id] : this.dataListSelections.map(item => {
          return item.id
        })
        this.$confirm(`确定对[id=${ids.join(',')}]进行[${id ? '禁用' : '批量禁用'}]操作?`, '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$http({
            url: this.$http.adornUrl('/store/close'),
            method: 'post',
            params: this.$http.adornParams({
              'id': id
            })
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
