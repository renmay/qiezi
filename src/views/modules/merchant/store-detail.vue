<template>
  <el-dialog
    :title="!dataForm.id ? '无数据' : '用户详情'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
      <el-form-item label="店铺名称" prop="name">
        <el-input v-model="dataForm.name" placeholder="" disabled></el-input>
      </el-form-item>
      <el-form-item label="商家" prop="merchantName">
        <el-input v-model="dataForm.merchantName" placeholder="" disabled></el-input>
      </el-form-item>
      <el-form-item label="商家手机" prop="merchantMobile">
        <el-input v-model="dataForm.merchantMobile" placeholder="" disabled></el-input>
      </el-form-item>
      <el-form-item label="简短名称" prop="shortName">
        <el-input v-model="dataForm.shortName" placeholder="" disabled></el-input>
      </el-form-item>
      <el-form-item label="简介" prop="intro">
        <el-input v-model="dataForm.intro" placeholder="" disabled></el-input>
      </el-form-item>
      <el-form-item label="预定须知" prop="notice">
        <el-input v-model="dataForm.notice" placeholder="" disabled></el-input>
      </el-form-item>
      <el-form-item label="创建时间" prop="createTime">
        <el-input v-model="dataForm.createTime" placeholder="" disabled></el-input>
      </el-form-item>
      <el-form-item label="前台电话" prop="receptionTel">
        <el-input v-model="dataForm.receptionTel" placeholder="" disabled></el-input>
      </el-form-item>
      <el-form-item label="前台手机" prop="receptionMobile">
        <el-input v-model="dataForm.receptionMobile" placeholder="" disabled></el-input>
      </el-form-item>
      <el-form-item label="邮箱" prop="email">
        <el-input v-model="dataForm.email" placeholder="" disabled></el-input>
      </el-form-item>
      <el-form-item label="传真" prop="fax">
        <el-input v-model="dataForm.fax" placeholder="" disabled></el-input>
      </el-form-item>
      <el-form-item label="邮编" prop="postCode">
        <el-input v-model="dataForm.postCode" placeholder="" disabled></el-input>
      </el-form-item>
      <el-form-item label="详细地址" prop="address">
        <el-input v-model="dataForm.address" placeholder="" disabled></el-input>
      </el-form-item>
      <el-form-item label="经度" prop="longitude">
        <el-input v-model="dataForm.longitude" placeholder="" disabled></el-input>
      </el-form-item>
      <el-form-item label="纬度" prop="latitude">
        <el-input v-model="dataForm.latitude" placeholder="" disabled></el-input>
      </el-form-item>
      <el-form-item label="交通指南" prop="traffic">
        <el-input v-model="dataForm.traffic" placeholder="" disabled></el-input>
      </el-form-item>
      <el-form-item label="状态" prop="status">
        <el-input v-model="dataForm.status" placeholder="" disabled></el-input>
      </el-form-item>
      <el-form-item label="是否营业" prop="open">
        <el-input v-model="dataForm.open" placeholder="" disabled></el-input>
      </el-form-item>
      <el-form-item label="开始营业时间" prop="beginTime">
        <el-input v-model="dataForm.beginTime" placeholder="" disabled></el-input>
      </el-form-item>
      <el-form-item label="结束营业时间" prop="endTime">
        <el-input v-model="dataForm.endTime" placeholder="" disabled></el-input>
      </el-form-item>
      <el-form-item label="成交量" prop="sales">
        <el-input v-model="dataForm.sales" placeholder="" disabled></el-input>
      </el-form-item>
      <el-form-item label="访问量" prop="clickCount">
        <el-input v-model="dataForm.clickCount" placeholder="" disabled></el-input>
      </el-form-item>
      <el-form-item label="商品数" prop="count">
        <el-input v-model="dataForm.count" placeholder="" disabled></el-input>
      </el-form-item>
      <el-form-item label="评价数量" prop="commentCount">
        <el-input v-model="dataForm.commentCount" placeholder="" disabled></el-input>
      </el-form-item>
      <el-form-item label="评分" prop="grade">
        <el-input v-model="dataForm.grade" placeholder="" disabled></el-input>
      </el-form-item>
      <el-form-item label="服务" prop="serve">
        <el-input v-model="dataForm.serve" placeholder="" disabled></el-input>
      </el-form-item>
      <el-form-item label="游戏质量" prop="material">
        <el-input v-model="dataForm.material" placeholder="" disabled></el-input>
      </el-form-item>
      <el-form-item label="价格合理" prop="price">
        <el-input v-model="dataForm.price" placeholder="" disabled></el-input>
      </el-form-item>
      <el-form-item label="省" prop="provinceId">
        <el-input v-model="dataForm.provinceId" placeholder="" disabled></el-input>
      </el-form-item>
      <el-form-item label="市" prop="cityId">
        <el-input v-model="dataForm.cityId" placeholder="" disabled></el-input>
      </el-form-item>
      <el-form-item label="区" prop="areaId">
        <el-input v-model="dataForm.areaId" placeholder="" disabled></el-input>
      </el-form-item>
      <el-form-item label="商圈" prop="tradingArea">
        <el-input v-model="dataForm.tradingArea" placeholder="" disabled></el-input>
      </el-form-item>
      <el-form-item label="首图" prop="titleImage">
        <el-input v-model="dataForm.titleImage" placeholder="" disabled></el-input>
      </el-form-item>
      <el-form-item label="店铺图片" prop="images">
        <el-input v-model="dataForm.images" placeholder="" disabled></el-input>
      </el-form-item>
      <el-form-item label="状态" size="mini" prop="status">
        <el-radio-group v-model="dataForm.status">
          <el-radio :label="2" disabled>已禁用</el-radio>
          <el-radio :label="1" disabled>正常</el-radio>
        </el-radio-group>
      </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button type="primary" @click="visible = false">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
  export default {
    data () {
      return {
        visible: false,
        dataForm: {
          id: 0,
          merchantName: '',
          merchantMobile: '',
          name: '',
          shortName: '',
          intro: '',
          notice: '',
          createTime: '',
          receptionTel: '',
          receptionMobile: '',
          email: '',
          fax: '',
          postCode: '',
          address: '',
          longitude: '',
          latitude: '',
          traffic: '',
          status: '',
          open: '',
          beginTime: '',
          endTime: '',
          sales: '',
          clickCount: '',
          count: '',
          commentCount: '',
          grade: '',
          serve: '',
          material: '',
          price: '',
          provinceId: '',
          cityId: '',
          areaId: '',
          tradingArea: '',
          titleImage: '',
          images: '',
          otherDataJson: ''
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.id = id
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/store?id=${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 200) {
                this.dataForm.merchantName = data.data.merchantName
                this.dataForm.merchantMobile = data.data.merchantMobile
                this.dataForm.name = data.data.name
                this.dataForm.shortName = data.data.shortName
                this.dataForm.intro = data.data.intro
                this.dataForm.notice = data.data.notice
                this.dataForm.createTime = data.data.createTime
                this.dataForm.receptionTel = data.data.receptionTel
                this.dataForm.receptionMobile = data.data.receptionMobile
                this.dataForm.email = data.data.email
                this.dataForm.fax = data.data.fax
                this.dataForm.postCode = data.data.postCode
                this.dataForm.address = data.data.address
                this.dataForm.longitude = data.data.longitude
                this.dataForm.latitude = data.data.latitude
                this.dataForm.traffic = data.data.traffic
                this.dataForm.status = data.data.status
                this.dataForm.open = data.data.open
                this.dataForm.beginTime = data.data.beginTime
                this.dataForm.endTime = data.data.endTime
                this.dataForm.sales = data.data.sales
                this.dataForm.clickCount = data.data.clickCount
                this.dataForm.count = data.data.count
                this.dataForm.commentCount = data.data.commentCount
                this.dataForm.grade = data.data.grade
                this.dataForm.serve = data.data.serve
                this.dataForm.material = data.data.material
                this.dataForm.price = data.data.price
                this.dataForm.provinceId = data.data.provinceId
                this.dataForm.cityId = data.data.cityId
                this.dataForm.areaId = data.data.areaId
                this.dataForm.tradingArea = data.data.tradingArea
                this.dataForm.titleImage = data.data.titleImage
                this.dataForm.images = data.data.images
                this.dataForm.otherDataJson = data.data.otherDataJson
              }
            })
          }
        })
      }
    }
  }
</script>
