<template>
  <div class="mod-demo-echarts">
    <!--<el-alert-->
    <!--title="提示："-->
    <!--type="warning"-->
    <!--:closable="false">-->
    <!--<div slot-scope="description">-->
    <!--<p class="el-alert__description">大数据界面展示</p>-->
    <!--</div>-->
    <!--</el-alert>-->
    <!--<el-card>装卡片的</el-card>-->
    <div class="container">
      <el-row :gutter="20">
        <el-col :span="8">
          <div id="J_chartLineBox" class="box chart-box"></div>
          <div id="box3" class="box mid-chart-box"></div>
          <div id="box4" class="box mid-chart-box">
            <div class="text-box">
              <title>标题1</title>
              <span>jdiw</span>
            </div>
            <div class="text-box">2</div>
            <div class="text-box">3</div>
          </div>

        </el-col>
        <el-col :span="9">
          <div id="J_chartPieBox" class="box big-chart-box"></div>
          <div id="J_chartScatterBox" class="box chart-box"></div>
        </el-col>

        <el-col :span="7">
          <div id="J_chartBarBox" class="box chart-box"></div>
          <el-col :span="12">
            <div id="small-box2" class="small-chart-box"></div>
          </el-col>
          <el-col :span="12">
            <div id="smallBox1" class="small-chart-box"></div>
          </el-col>
          <div class="chart-box table">
            <h3>热销商品</h3>
            <el-table
              :data="tableData"
              align="center"
              style="width: 100%">
              <el-table-column align="center"
                               prop="date"
                               label="日期"
                               width="180">
              </el-table-column>
              <el-table-column align="center"
                               prop="name"
                               label="姓名"
                               width="180">
              </el-table-column>
              <el-table-column align="center"
                               prop="address"
                               label="地址">
              </el-table-column>
            </el-table>
          </div>
        </el-col>
      </el-row>
    </div>
  </div>
</template>

<script>
  import echarts from 'echarts'
  // import theme from 'echarts/theme/macarons.js'

  export default {
    data () {
      return {
        chartLine: null,
        chartBar: null,
        chartPie: null,
        chartScatter: null,
        tableData: [{
          date: '2016-05-02',
          name: '王小虎',
          address: '上海市普陀区金沙江路 1518 弄'
        }, {
          date: '2016-05-04',
          name: '王小虎',
          address: '上海市普陀区金沙江路 1517 弄'
        }, {
          date: '2016-05-01',
          name: '王小虎',
          address: '上海市普陀区金沙江路 1519 弄'
        }, {
          date: '2016-05-03',
          name: '王小虎',
          address: '上海市普陀区金沙江路 1516 弄'
        }]
      }
    },
    mounted () {
      this.initChartLine()
      this.initChartBar()
      this.initChartPie()
      this.initChartScatter()
      this.initSmallBox1()
      this.initSmallBox2()
      this.initBox3()
    },
    activated () {
      // 由于给echart添加了resize事件, 在组件激活时需要重新resize绘画一次, 否则出现空白bug
      if (this.chartLine) {
        this.chartLine.resize()
      }
      if (this.chartBar) {
        this.chartBar.resize()
      }
      if (this.chartPie) {
        this.chartPie.resize()
      }
      if (this.chartScatter) {
        this.chartScatter.resize()
      }
    },
    methods: {
      // 折线图
      initChartLine () {
        var option = {
          'title': {
            'subtext': '折线图堆叠',
            textStyle: {
              fontWeight: 'normal',
              color: 'rgb(203, 227, 242)'
            }
          },
          'tooltip': {
            'trigger': 'axis'
          },
          'legend': {
            textStyle: {
              color: '#ccc'          // 图例文字颜色
            },
            'data': ['邮件营销', '联盟广告', '视频广告', '直接访问', '搜索引擎']
          },
          'grid': {
            'left': '3%',
            'right': '4%',
            'bottom': '3%',
            'containLabel': true
          },
          'xAxis': {
            'type': 'category',
            'boundaryGap': true,
            'splitLine': false,
            axisLabel: {
              show: true,
              textStyle: {
                'color': '#689ED0'
              }
            },
            // y轴的颜色和宽度
            axisLine: {
              lineStyle: {
                color: '#689ED0',
                width: '1'                 // 这里是坐标轴的宽度
              }
            },
            'data': ['周一', '周二', '周三', '周四', '周五', '周六', '周日']
          },
          'yAxis': {
            'type': 'value',
            'splitLine': false,
            axisLabel: {
              show: true,
              textStyle: {
                'color': '#689ED0'
              }
            },
            // y轴的颜色和宽度
            axisLine: {
              lineStyle: {
                color: '#689ED0',
                width: '1'                  // 这里是坐标轴的宽度
              }
            }
          },
          'series': [
            {
              'name': '邮件营销',
              'type': 'line',
              'stack': '总量',
              itemStyle: {
                color: '#3C90EC'
              },
              'data': [120, 132, 101, 134, 90, 230, 210]
            },
            {
              'name': '直接访问',
              'type': 'line',
              'stack': '总量',
              'data': [320, 332, 301, 334, 390, 330, 320]
            },
            {
              'name': '搜索引擎',
              'type': 'line',
              'stack': '总量',
              itemStyle: {
                color: '#3C90EC'
              },
              'data': [820, 932, 901, 934, 1290, 1330, 1320]
            }
          ]
        }
        this.chartLine = echarts.init(document.getElementById('J_chartLineBox'))
        this.chartLine.setOption(option)
        window.addEventListener('resize', () => {
          this.chartLine.resize()
        })
      },

      // 柱状图
      initChartBar () {
        var option = {
          tooltip: {
            trigger: 'axis',
            axisPointer: {
              type: 'shadow'
            }
          },
          legend: {
            textStyle: {
              color: '#ccc'          // 图例文字颜色
            },
            data: ['直接访问', '邮件营销', '联盟广告', '视频广告', '搜索引擎', '百度', '谷歌', '必应', '其他']
          },
          grid: {
            left: '3%',
            right: '4%',
            bottom: '3%',
            containLabel: true
          },
          xAxis: [
            {
              type: 'category',
              splitLine: false,
              data: ['周一', '周二', '周三', '周四', '周五', '周六', '周日'],
              axisLabel: {
                show: true,
                textStyle: {
                  color: '#689ED0'
                }
              },
              // y轴的颜色和宽度
              axisLine: {
                lineStyle: {
                  color: '#689ED0',
                  width: 1                 // 这里是坐标轴的宽度
                }
              }
            }
          ],
          yAxis: [
            {
              type: 'value',
              splitLine: false,
              axisLabel: {
                show: true,
                textStyle: {
                  color: '#689ED0'
                }
              },
              // y轴的颜色和宽度
              axisLine: {
                lineStyle: {
                  color: '#689ED0',
                  width: 1                  // 这里是坐标轴的宽度
                }
              }
            }
          ],
          series: [
            {
              name: '直接访问',
              type: 'bar',
              barWidth: 12,
              itemStyle: {
                // 柱形图圆角，鼠标移上去效果，如果只是一个数字则说明四个参数全部设置为那么多
                emphasis: {
                  barBorderRadius: 50
                },
                normal: {
                  // 柱形图圆角，初始化效果
                  barBorderRadius: [20, 20, 20, 20],
                  color: new echarts.graphic.LinearGradient(
                    0, 0, 0, 1,
                    [
                      {offset: 0, color: '#43eec6'}
                    ]
                  )
                }
              },
              data: [320, 332, 301, 334, 390, 330, 320]
            },
            {
              name: '搜索引擎',
              type: 'bar',
              barWidth: 12,
              data: [862, 1018, 964, 1026, 1679, 1600, 1570],
              itemStyle: {
                // 柱形图圆角，鼠标移上去效果，如果只是一个数字则说明四个参数全部设置为那么多
                normal: {
                  barBorderRadius: 50,
                  color: new echarts.graphic.LinearGradient(
                    0, 0, 0, 1,
                    [
                      {offset: 0, color: '#24528c'},
                      {offset: 1, color: '#43eec6'}
                    ]
                  )
                }
              },
              markLine: {
                lineStyle: {
                  normal: {
                    type: 'dashed'
                  }
                },
                data: [
                  [{type: 'min'}, {type: 'max'}]
                ]
              }
            }
          ]
        }
        this.chartBar = echarts.init(document.getElementById('J_chartBarBox'))
        this.chartBar.setOption(option)
        window.addEventListener('resize', () => {
          this.chartBar.resize()
        })
      },

      // 饼状图
      initChartPie () {
        var option = {
          title: {
            subtext: 'Customized Pie',
            left: 'center',
            top: 20,
            textStyle: {
              color: '#ccc'
            }
          },
          tooltip: {
            trigger: 'item',
            formatter: '{a} <br/>{b} : {c} ({d}%)'
          },
          visualMap: {
            show: false,
            min: 80,
            max: 600,
            inRange: {
              colorLightness: [0, 1]
            }
          },
          series: [
            {
              name: '访问来源',
              type: 'pie',
              radius: '55%',
              center: ['50%', '50%'],
              data: [
                {value: 335, name: '直接访问'},
                {value: 310, name: '邮件营销'},
                {value: 274, name: '联盟广告'},
                {value: 235, name: '视频广告'},
                {value: 400, name: '搜索引擎'}
              ].sort(function (a, b) {
                return a.value - b.value
              }),
              roseType: 'radius',
              label: {
                normal: {
                  textStyle: {
                    color: 'rgba(255, 255, 255, 0.3)'
                  }
                }
              },
              labelLine: {
                normal: {
                  lineStyle: {
                    color: 'rgba(255, 255, 255, 0.3)'
                  },
                  smooth: 0.2,
                  length: 10,
                  length2: 20
                }
              },
              itemStyle: {
                normal: {
                  // color: '#3c90ec',
                  color: '#24528c',
                  shadowBlur: 200,
                  shadowColor: 'rgba(0, 0, 0, 0.5)'
                }
              },
              animationType: 'scale',
              animationEasing: 'elasticOut',
              animationDelay: function (idx) {
                return Math.random() * 200
              }
            }
          ]
        }
        this.chartPie = echarts.init(document.getElementById('J_chartPieBox'))
        this.chartPie.setOption(option)
        window.addEventListener('resize', () => {
          this.chartPie.resize()
        })
      },

      // 散点图
      initChartScatter () {
        var option = {
          // backgroundColor: new echarts.graphic.RadialGradient(0.3, 0.3, 0.8, [
          //   { offset: 0, color: '#f7f8fa' },
          //   { offset: 1, color: '#cdd0d5' }
          // ]),
          title: {
            subtext: '1990 与 2015 年各国家人均寿命与 GDP'
          },
          legend: {
            textStyle: {
              color: '#ccc'          // 图例文字颜色
            },
            right: 10,
            data: ['1990', '2015']
          },
          xAxis: {
            splitLine: false,
            axisLabel: {
              show: true,
              textStyle: {
                color: '#689ED0'
              }
            },
            // y轴的颜色和宽度
            axisLine: {
              lineStyle: {
                color: '#689ED0',
                width: 1                  // 这里是坐标轴的宽度
              }
            }
          },
          yAxis: {
            splitLine: false,
            scale: true,
            axisLabel: {
              show: true,
              textStyle: {
                color: '#689ED0'
              }
            },
            // y轴的颜色和宽度
            axisLine: {
              lineStyle: {
                color: '#689ED0',
                width: 1                  // 这里是坐标轴的宽度
              }
            }
          },
          series: [
            {
              name: '1990',
              data: [
                [28604, 77, 17096869, 'Australia', 1990],
                [31163, 77.4, 27662440, 'Canada', 1990],
                [1516, 68, 1154605773, 'China', 1990],
                [13670, 74.7, 10582082, 'Cuba', 1990],
                [28599, 75, 4986705, 'Finland', 1990],
                [29476, 77.1, 56943299, 'France', 1990],
                [31476, 75.4, 78958237, 'Germany', 1990],
                [28666, 78.1, 254830, 'Iceland', 1990],
                [1777, 57.7, 870601776, 'India', 1990],
                [29550, 79.1, 122249285, 'Japan', 1990],
                [2076, 67.9, 20194354, 'North Korea', 1990],
                [12087, 72, 42972254, 'South Korea', 1990],
                [24021, 75.4, 3397534, 'New Zealand', 1990],
                [43296, 76.8, 4240375, 'Norway', 1990],
                [10088, 70.8, 38195258, 'Poland', 1990],
                [19349, 69.6, 147568552, 'Russia', 1990],
                [10670, 67.3, 53994605, 'Turkey', 1990],
                [26424, 75.7, 57110117, 'United Kingdom', 1990],
                [37062, 75.4, 252847810, 'United States', 1990]
              ],
              type: 'scatter',
              symbolSize: function (data) {
                return Math.sqrt(data[2]) / 5e2
              },
              label: {
                emphasis: {
                  show: true,
                  formatter: function (param) {
                    return param.data[3]
                  },
                  position: 'top'
                }
              },
              itemStyle: {
                normal: {
                  shadowBlur: 10,
                  shadowColor: 'rgba(120, 36, 50, 0.5)',
                  shadowOffsetY: 5,
                  color: new echarts.graphic.RadialGradient(0.4, 0.3, 1, [
                    {offset: 0, color: 'rgb(251, 118, 123)'},
                    {offset: 1, color: 'rgb(204, 46, 72)'}
                  ])
                }
              }
            },
            {
              name: '2015',
              data: [
                [44056, 81.8, 23968973, 'Australia', 2015],
                [43294, 81.7, 35939927, 'Canada', 2015],
                [13334, 76.9, 1376048943, 'China', 2015],
                [21291, 78.5, 11389562, 'Cuba', 2015],
                [38923, 80.8, 5503457, 'Finland', 2015],
                [37599, 81.9, 64395345, 'France', 2015],
                [44053, 81.1, 80688545, 'Germany', 2015],
                [42182, 82.8, 329425, 'Iceland', 2015],
                [5903, 66.8, 1311050527, 'India', 2015],
                [36162, 83.5, 126573481, 'Japan', 2015],
                [1390, 71.4, 25155317, 'North Korea', 2015],
                [34644, 80.7, 50293439, 'South Korea', 2015],
                [34186, 80.6, 4528526, 'New Zealand', 2015],
                [64304, 81.6, 5210967, 'Norway', 2015],
                [24787, 77.3, 38611794, 'Poland', 2015],
                [23038, 73.13, 143456918, 'Russia', 2015],
                [19360, 76.5, 78665830, 'Turkey', 2015],
                [38225, 81.4, 64715810, 'United Kingdom', 2015],
                [53354, 79.1, 321773631, 'United States', 2015]
              ],
              type: 'scatter',
              symbolSize: function (data) {
                return Math.sqrt(data[2]) / 5e2
              },
              label: {
                emphasis: {
                  show: true,
                  formatter: function (param) {
                    return param.data[3]
                  },
                  position: 'top'
                }
              },
              itemStyle: {
                normal: {
                  shadowBlur: 10,
                  shadowColor: 'rgba(25, 100, 150, 0.5)',
                  shadowOffsetY: 5,
                  color: new echarts.graphic.RadialGradient(0.4, 0.3, 1, [
                    {offset: 0, color: 'rgb(129, 227, 238)'},
                    {offset: 1, color: 'rgb(25, 183, 207)'}
                  ])
                }
              }
            }
          ]
        }
        this.chartPie = echarts.init(document.getElementById('J_chartScatterBox'))
        this.chartPie.setOption(option)
        window.addEventListener('resize', () => {
          this.chartPie.resize()
        })
      },

      // 降水量
      initSmallBox1 () {
        var colors = ['#3c8d8d', '#D84557', '#689ED0']
        var option = {
          color: colors,
          tooltip: {
            trigger: 'axis',
            axisPointer: {
              type: 'cross'
            }
          },
          grid: {
            right: '20%',
            left: '20%'
          },
          legend: {
            textStyle: {
              color: '#ccc'          // 图例文字颜色
            },
            data: ['蒸发量', '降水量', '平均温度']
          },
          xAxis: [
            {
              type: 'category',
              splitLine: false,
              axisTick: {
                alignWithLabel: true
              },
              data: ['1月', '2月', '3月', '4月', '5月', '6月', '7月', '8月', '9月', '10月', '11月', '12月']
            }
          ],
          yAxis: [
            {
              type: 'value',
              name: '蒸发量',
              min: 0,
              max: 250,
              position: 'right',
              splitLine: false,
              axisLine: {
                lineStyle: {
                  color: colors[0]
                }
              },
              axisLabel: {
                formatter: '{value} ml'
              }
            },
            {
              type: 'value',
              name: '降水量',
              min: 0,
              max: 250,
              position: 'right',
              splitLine: false,
              offset: 80,
              axisLine: {
                lineStyle: {
                  color: colors[1]
                }
              },
              axisLabel: {
                formatter: '{value} ml'
              }
            },
            {
              type: 'value',
              name: '温度',
              min: 0,
              max: 25,
              position: 'left',
              splitLine: false,
              axisLine: {
                lineStyle: {
                  color: colors[2]
                }
              },
              axisLabel: {
                formatter: '{value} °C'
              }
            }
          ],
          series: [
            {
              name: '蒸发量',
              type: 'bar',
              barWidth: 6,
              itemStyle: {
                // 柱形图圆角，鼠标移上去效果，如果只是一个数字则说明四个参数全部设置为那么多
                emphasis: {
                  barBorderRadius: 50
                },
                normal: {
                  // 柱形图圆角，初始化效果
                  barBorderRadius: [20, 20, 20, 20]
                }
              },
              data: [2.0, 4.9, 7.0, 23.2, 25.6, 76.7, 135.6, 162.2, 32.6, 20.0, 6.4, 3.3]
            },
            {
              name: '降水量',
              type: 'bar',
              yAxisIndex: 1,
              barWidth: 6,
              itemStyle: {
                // 柱形图圆角，鼠标移上去效果，如果只是一个数字则说明四个参数全部设置为那么多
                emphasis: {
                  barBorderRadius: 50
                },
                normal: {
                  // 柱形图圆角，初始化效果
                  barBorderRadius: [20, 20, 20, 20]
                }
              },
              data: [2.6, 5.9, 9.0, 26.4, 28.7, 70.7, 175.6, 182.2, 48.7, 18.8, 6.0, 2.3]
            },
            {
              name: '平均温度',
              type: 'line',
              yAxisIndex: 2,
              data: [2.0, 2.2, 3.3, 4.5, 6.3, 10.2, 20.3, 23.4, 23.0, 16.5, 12.0, 6.2]
            }
          ]
        }
        this.chartLine = echarts.init(document.getElementById('smallBox1'))
        this.chartLine.setOption(option, true)
        window.addEventListener('resize', () => {
          this.chartLine.resize()
        })
      },

      // 今日上线用户占比
      initSmallBox2 () {
        var option = {
          color: ['#66e2e2', '#3c90ec', '#D84557'],
          tooltip: {
            trigger: 'item',
            formatter: '{a} <br/>{b}: {c} ({d}%)'
          },
          title: {
            subtext: '今日上线用户占比',
            left: 'center'
          },
          series: [
            {
              name: '访问来源',
              type: 'pie',
              radius: ['50%', '70%'],
              avoidLabelOverlap: false,
              label: {
                normal: {
                  show: false,
                  position: 'center'
                },
                emphasis: {
                  show: true,
                  textStyle: {
                    fontSize: '30',
                    fontWeight: 'bold'
                  }
                }
              },
              labelLine: {
                normal: {
                  show: false
                }
              },
              data: [
                {value: 335, name: '直接访问'},
                {value: 310, name: '邮件营销'},
                {value: 234, name: '联盟广告'},
                {value: 135, name: '视频广告'},
                {value: 1548, name: '搜索引擎'}
              ]
            }
          ]
        }
        this.chartLine = echarts.init(document.getElementById('small-box2'))
        this.chartLine.setOption(option)
        window.addEventListener('resize', () => {
          this.chartLine.resize()
        })
      },

      // 堆叠区域图
      initBox3: function () {
        var option
        option = {
          title: {
            subtext: '堆叠区域图'
          },
          tooltip: {
            trigger: 'axis',
            axisPointer: {
              type: 'cross',
              label: {
                backgroundColor: '#6a7985'
              }
            }
          },
          legend: {
            textStyle: {
              color: '#ccc'
            },
            data: ['邮件营销', '联盟广告', '视频广告', '直接访问', '搜索引擎']
          },
          toolbox: {
            feature: {
              saveAsImage: {}
            }
          },
          grid: {
            left: '3%',
            right: '4%',
            bottom: '3%',
            containLabel: true
          },
          xAxis: [
            {
              type: 'category',
              boundaryGap: false,
              splitLine: false,
              axisLabel: {
                show: true,
                textStyle: {
                  color: '#689ED0'
                }
              },
              // y轴的颜色和宽度
              axisLine: {
                lineStyle: {
                  color: '#689ED0',
                  width: 1                  // 这里是坐标轴的宽度
                }
              },
              data: ['周一', '周二', '周三', '周四', '周五', '周六', '周日']
            }
          ],
          yAxis: [
            {
              type: 'value',
              splitLine: false,
              axisLabel: {
                show: true,
                textStyle: {
                  color: '#689ED0'
                }
              },
              // y轴的颜色和宽度
              axisLine: {
                lineStyle: {
                  color: '#689ED0',
                  width: 1                 // 这里是坐标轴的宽度
                }
              }
            }
          ],
          series: [
            {
              name: '邮件营销',
              type: 'line',
              stack: '总量',
              areaStyle: {
                normal: {
                  color: '#43eec6'
                }
              },
              data: [120, 132, 101, 134, 90, 230, 210]
            },
            {
              name: '直接访问',
              type: 'line',
              stack: '总量',
              areaStyle: {
                normal: {
                  color: '#E495B7'
                }
              },
              data: [320, 332, 301, 334, 390, 330, 320]
            },
            {
              name: '搜索引擎',
              type: 'line',
              stack: '总量',
              label: {
                normal: {
                  show: true,
                  position: 'top'
                }
              },
              areaStyle: {
                normal: {
                  color: '#3C90EC'
                }
              },
              data: [820, 932, 901, 934, 1290, 1330, 1320]
            }
          ]
        }

        this.chartPie = echarts.init(document.getElementById('box3'))
        this.chartPie.setOption(option)
        window.addEventListener('resize', () => {
          this.chartPie.resize()
        })
      }
    }
  }
</script>

<style lang="scss">
  .mod-demo-echarts {
    .container {
      padding: 16px;
    }
    background-color: #142734;
    /*使用渐变*/
    /*background: -webkit-linear-gradient(left, rgb(55, 77, 112), #142734); !* Safari 5.1 - 6.0 *!*/
    /*background: -o-linear-gradient(right, #66E2E2, #142734); !* Opera 11.1 - 12.0 *!*/
    /*background: -moz-linear-gradient(right, #66E2E2, #142734); !* Firefox 3.6 - 15 *!*/
    /*background: linear-gradient(to right, #66E2E2, #142734); !* 标准的语法 *!*/
    /*使用背景图*/
    /*background-image: url('../../assets/img/echarts.jpg');*/
    /*background-repeat: repeat;*/
    /*-webkit-background-size: 100%;*/
    /*background-size: 100%;*/
    /*background-color:rgba(0,0,0,0.1);*/
    > .el-alert {
      margin-bottom: 10px;
    }
    > .el-row {
      margin-top: -10px;
      margin-bottom: -10px;
      .el-col {
        padding-top: 10px;
        padding-bottom: 10px;
      }
    }
    .box {
      padding: 10px;
      margin: 10px;
      border: 1px solid rgba(43, 68, 91, 0.97);
    }
    .chart-box {
      min-height: 538px;
    }
    .small-chart-box {
      min-height: 260px;
      margin-bottom: 20px;
      border: 1px solid rgba(43, 68, 91, 0.97);
    }
    .mid-chart-box {
      min-height: 300px;
    }
    .big-chart-box {
      min-height: 600px;
    }
    .box4{
    }
    .text-box {
      float: left;
      padding:0 2%;
      width: 31%;
      margin: 60px 10px 20px 0;
      min-height: 120px;
      background-color: #24528C;
      /*background-image: linear-gradient(to right,rgba(255,255,255,0), rgba(156, 156, 156, 0.22));*/

      /*background: -webkit-linear-gradient(left, rgba(53, 157, 123, 0.62), #4cb1b1); !* Safari 5.1 - 6.0 *!*/
      /*background: -o-linear-gradient(right, #66E2E2, #4cb1b1); !* Opera 11.1 - 12.0 *!*/
      /*background: -moz-linear-gradient(right, #66E2E2, #4cb1b1); !* Firefox 3.6 - 15 *!*/
      /*background: linear-gradient(to right, #66E2E2, #4cb1b1); !* 标准的语法 *!*/
      color: rgba(37, 127, 103, 0.1)
    }
    .table {
      text-align: center;

      tr {
        background-color: #142734;
        border-bottom: none;
        color: #24528C;
        font-weight: lighter;
      }
      td {
        border-bottom: none;
        background-color: #142734;
        color: #24528C;
        font-weight: lighter;
      }
      th {
        background-color: #142734;
        color: #24528C;
        border-bottom: none;
      }
      h3 {
        font-weight: lighter;
        color:#ccc;
        padding-bottom: 14px;
        font-size: 14px;
      }
      .el-table:before {
        height: 0;
      }

    }
    .table:hover {
      background-color: #142734;
    }
  }
</style>
