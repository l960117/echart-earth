<template>
  <div id="app">
    <div id="main" style="width: 100%;height:100vh;"></div>
  </div>
</template>

<script>
import echarts from 'echarts'
import china from 'echarts/map/json/china.json'
export default {
  name: 'App',
  data () {
    return {
      geoCoordMap: {
        北京: [116.28, 39.54],
        重庆: [107.7539, 30.1904],
        上海: [121.4648, 31.2891]
      },
      BJData: [
        [{
          name: "北京",
          value: 12580
        }],[{
          name: "重庆",
          value: 10000000
        }],[{
          name: "上海",
          value: 9100
        }]
      ],
      chart: null
    }
  },
  mounted () {
    this.initEarth()
  },
  methods: {
    initEarth () {
      this.chart = echarts.init(document.getElementById("main"));
      echarts.registerMap('china', china)
      const series = [];
      [[, this.BJData]].forEach(item => {
        series.push({
          type: 'effectScatter',
          coordinateSystem: 'geo',
          zlevel: 2,
          rippleEffect: {
            //涟漪特效
            period: 4, //动画时间，值越小速度越快
            brushType: 'stroke', //波纹绘制方式 stroke, fill
            scale: 4
            //波纹圆环最大限制，值越大波纹越大
          },
          label: {
            normal: {
              show: true,
              position: 'right', //显示位置
              offset: [5, 0], //偏移设置
              formatter: '{b}' //圆环显示文字
            },
            emphasis: {
              show: true
            }
          },
          symbol: 'circle',
          symbolSize: function (val) {
            var  level = 0
            var state= Math.floor(val[2]/5000)
            switch (state) {
              case 0: 
                level=0
                break
              case 1:
                level=1
                break
              case 2:
                level=2
                break
              case 3:
                level=3
                break
              case 4:
                level=4
                break
              case 5:
                level=5
                break
              case 6:
                level=6
                break
              case 7:
                level=7
                break
              case 8:
                level=8
                break
              case 9:
                level=9
                break
              default:
                level=10
            }
            return 5 + level //圆环大小
          },
          data: item[1].map((dataItem) => {
            return {
              name: dataItem[0].name/*+"\n"+dataItem[0].value*/,
              value: this.geoCoordMap[dataItem[0].name]
                  .concat([dataItem[0].value])
            }
          })
        })
      })
      const option = {
        backgroundColor: '#000',
        //悬浮提示
        tooltip: {
          trigger: "item",
          backgroundColor: "#1540a1",
          borderColor: "#FFFFCC",
          showDelay: 0,
          hideDelay: 0,
          // enterable: true,
          transitionDuration: 0,
          // extraCssText: "z-index:100",formatter
          formatter: function (params, ticket, callback) {
            //根据业务自己拓展要显示的内容
            var res = ''
            var name = params.name
            var value = params.value[params.seriesIndex + 1]
            res = '<span style="color:#fff;">' + name.toString().split(' ')[0]
                + '</span><br/>爬虫：' + name.toString().split(' ')[1]
            return res
          }
        },
        visualMap: {
          //图例值控制
          min: 0,
          max: 10000,
          text:['High','Low'],
          show: false,
          calculable: true,
          //color: ["#0bc7f3"],
          color: ['orangered','yellow','lightskyblue']
        },
        geo: {
          map: 'china',
          label: {
            emphasis: {
              show: false
            }
          },
          roam: true, //是否允许缩放
          layoutCenter: ['50%', '50%'], //地图位置
          layoutSize: '100%',
          itemStyle: {
            normal: {
              color: ["#04284e"], //地图背景色
              //color: ['orangered','yellow','lightskyblue']
              borderColor: "#5bc1c9" //省市边界线
            },
            emphasis: {
              color: "rgba(37, 43, 61, .5)" //悬浮背景
            }
          }
        },
        series: series
      };
      this.chart.setOption(option)
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
</style>
