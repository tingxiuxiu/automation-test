<template>
  <div :class="className" :style="{height:height,width:width}" />
</template>

<script>
import echarts from 'echarts'
require('echarts/theme/macarons') // echarts theme
import resize from './mixins/resize'
import { pieChartList } from '@/api/dashboard/data_search'

export default {
  mixins: [resize],
  props: {
    className: {
      type: String,
      default: 'chart'
    },
    width: {
      type: String,
      default: '100%'
    },
    height: {
      type: String,
      default: '300px'
    }
  },
  data() {
    return {
      chart: null
    }
  },
  // created() {
  //   this.getChartData()
  // },
  mounted() {
    this.$nextTick(() => {
      this.initChart()
    })
  },
  beforeDestroy() {
    if (!this.chart) {
      return
    }
    this.chart.dispose()
    this.chart = null
  },
  methods: {
    initChart() {
      this.chart = echarts.init(this.$el, 'macarons')
      this.chart.setOption({
        title: {
          text: '测试结果',
          left: 'left',
          top: 0,
          textStyle: {
            color: '#3e81b5'
          }
        },
        tooltip: {
          trigger: 'item',
          formatter: '{a} <br/>{b} : {c} ({d}%)'
        },
        legend: {
          left: 'center',
          bottom: '10',
          data: ['成功', '失败', '错误', '跳过']
        },
        color: ['#79e076', '#ea5555', '#ea8f40', '#a1a1a4'],
        series: [
          {
            name: '执行结果统计',
            type: 'pie',
            // roseType: 'radius',
            radius: [40, 90],
            center: ['50%', '38%'],
            avoidLabelOverlap: true,
            data: [],
            animationEasing: 'cubicInOut',
            animationDuration: 2600
          }
        ]
      })
      pieChartList().then(response => {
        this.chart.setOption({
          series: [
            {
              data: response.data
            }
          ]
        })
      })
    }
  }
}
</script>
