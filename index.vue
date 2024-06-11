<template>
  <div class="center-body">
    <div class="map" id="map"></div>
    <div class="rest-button" v-if="showButton" @click="reJiangSuMap()">返回江苏省</div>
  </div>
</template>

<script>
import * as echarts from "echarts";

export default {
  data () {
    return {
      showButton: true,//是否显示返回按钮
      currentCity: "",//当前城市
      currentCityPY: "",//当前城市拼音
      currentArea: "",//当前区县
      currentAreaPY: "",//当前区县拼音
      cityCoordinate: [//标点
        { name: "南京市", center: ['43%', '40%'] },
        { name: "无锡市", center: ['63%', '52%'] },
        { name: "徐州市", center: ['25%', '20%'] },
        { name: "常州市", center: ['55%', '68%'] },
        { name: "苏州市", center: ['78%', '70%'] },
        { name: "南通市", center: ['83%', '60%'] },
        { name: "连云港市", center: ['50%', '14%'] },
        { name: "淮安市", center: ['41%', '40%'] },
        { name: "盐城市", center: ['65%', '30%'] },
        { name: "扬州市", center: ['55%', '50%'] },
        { name: "泰州市", center: ['65%', '55%'] },
        { name: "宿迁市", center: ['41%', '25%'] },
        { name: "镇江市", center: ['55%', '60%'] },
      ],
      // 市json数据名称
      province: {
        "南京市": "nanjing",
        "无锡市": "wuxi",
        "徐州市": "xuzhou",
        "常州市": "changzhou",
        "苏州市": "suzhou",
        "南通市": "nantong",
        "连云港市": "lianyungang",
        "淮安市": "huaian",
        "盐城市": "yancheng",
        "扬州市": "yangzhou",
        "泰州市": "taizhou",
        "宿迁市": "suqian",
        "镇江市": "zhenjiang"
      },
      //区县json数据名称
      District: {
        "玄武区": "xuanwuqu",
        "秦淮区": "qinhuaiqu",
        "建邺区": "jianyequ",
        "鼓楼区": "gulouqu",
        "浦口区": "pukouqu",
        "栖霞区": "qixiaqu",
        "雨花台区": "yuhuataiqu",
        "江宁区": "jiangningqu",
        "六合区": "liuhequ",
        "溧水区": "lishuiqu",
        "高淳区": "gaochunqu",
        "锡山区": "xishanqu",
        "惠山区": "huishanqu",
        "滨湖区": "binhuqu",
        "梁溪区": "liangxiqu",
        "新吴区": "xinwuqu",
        "江阴市": "jiangyinshi",
        "宜兴市": "yixingshi",
        "鼓楼区": "gulouqu",
        "云龙区": "yunlongqu",
        "贾汪区": "jiawangqu",
        "泉山区": "quanshanqu",
        "铜山区": "tongshanqu",
        "丰县": "fengxian",
        "沛县": "peixian",
        "睢宁县": "suiningxian",
        "新沂市": "xinyishi",
        "邳州市": "pizhoushi",
        "天宁区": "tianningqu",
        "钟楼区": "zhonglouqu",
        "新北区": "xinbeiqu",
        "武进区": "wujinqu",
        "金坛区": "jintanqu",
        "溧阳市": "liyangshi",
        "虎丘区": "huqiuqu",
        "吴中区": "wuzhongqu",
        "相城区": "xiangchengqu",
        "姑苏区": "gusuqu",
        "吴江区": "wujiangqu",
        "常熟市": "changshushi",
        "张家港市": "zhangjiagangshi",
        "昆山市": "kunshanshi",
        "太仓市": "taicangshi",
        "崇川区": "chongchuanqu",
        "港闸区": "gangzhaqu",
        "通州区": "tongzhouqu",
        "如东县": "rudongxian",
        "启东市": "qidongshi",
        "如皋市": "rugaoshi",
        "海门区": "haimenqu",
        "海安市": "haianshi",
        "连云区": "lianyunqu",
        "海州区": "haizhouqu",
        "赣榆区": "ganyuqu",
        "东海县": "donghaixian",
        "灌云县": "guanyunxian",
        "灌南县": "guannanxian",
        "淮安区": "huaianqu",
        "淮阴区": "huaiyinqu",
        "清江浦区": "qingjiangpuqu",
        "洪泽区": "hongzequ",
        "涟水县": "lianshuixian",
        "盱眙县": "xuyixian",
        "金湖县": "jinhuxian",
        "亭湖区": "tinghuqu",
        "盐都区": "yanduqu",
        "大丰区": "dafengqu",
        "响水县": "xiangshuixian",
        "滨海县": "binhaixian",
        "阜宁县": "funingxian",
        "射阳县": "sheyangxian",
        "建湖县": "jianhuxian",
        "东台市": "dongtaishi",
        "广陵区": "guanglingqu",
        "邗江区": "hanjiangqu",
        "江都区": "jiangduqu",
        "宝应县": "baoyingxian",
        "仪征市": "yizhengshi",
        "高邮市": "gaoyoushi",
        "京口区": "jingkouqu",
        "润州区": "runzhouqu",
        "丹徒区": "dantuqu",
        "丹阳市": "danyangshi",
        "扬中市": "yangzhongshi",
        "句容市": "jurongshi",
        "海陵区": "hailingqu",
        "高港区": "gaogangqu",
        "姜堰区": "jiangyanqu",
        "兴化市": "xinghuashi",
        "靖江市": "jingjiangshi",
        "泰兴市": "taixingshi",
        "宿城区": "suchengqu",
        "宿豫区": "suyuqu",
        "沭阳县": "shuyangxian",
        "泗阳县": "siyangxian",
        "泗洪县": "sihongxian"
      },
      //地图展示数据
      mapData: [
        {
          name: '南京市',
          value: '20',
          pieValueData: [
            { value: "1048", name: '养殖', itemStyle: { color: '#FEDB5F' } },
            { value: "735", name: '种植', itemStyle: { color: '#9FE080' } },
            { value: "580", name: '三产业融合', itemStyle: { color: '#5C7BD9' } },
          ],
          mapArea: [
            { name: '玄武区', value: '3' },
            { name: '秦淮区', value: '100' },
            { name: '建邺区', value: '150' },
            { name: '鼓楼区', value: '200' },
            { name: '浦口区', value: '300' },
            { name: '栖霞区', value: '400' },
            { name: '雨花台区', value: '500' },
            { name: '江宁区', value: '600' },
            { name: '六合区', value: '700' },
            { name: '溧水区', value: '800' },
            { name: '高淳区', value: '800' },
          ],
          markPoints: [
            { name: '南京雨花茶', coord: [118.701323, 31.870489],},
          ]
        },
        {
          name: '宿迁市',
          value: '860',
          pieValueData: [
            { value: "148", name: '养殖', itemStyle: { color: '#FEDB5F' } },
            { value: "735", name: '种植', itemStyle: { color: '#9FE080' } },
            { value: "580", name: '三产业融合', itemStyle: { color: '#5C7BD9' } }],
          mapArea: [
            { name: "宿城区", value: '31', value2: 700, value3: 120, },
            { name: "宿豫区", value: '10' },
            { name: "沭阳县", value: '150' },
            { name: "泗阳县", value: '200' },
            { name: "泗洪县", value: '300' },

          ]
        },

      ],




    };
  },
  created () {
  },
  mounted () {


    this.reJiangSuMap()

  },
  computed: {

  },
  methods: {
    drawMap (name, json) {
      // 判断地图是否渲染
      let myChart = echarts.getInstanceByDom(document.getElementById("map"))
      // 如果渲染则清空地图 
      if (myChart != null) {
        myChart.dispose()
      }
      // 初始化地图
      myChart = echarts.init(document.getElementById("map"));
      if (!json) {
        json = require("@/jsMapJson/jiangsu.json");
        this.showButton = false
      }

      echarts.registerMap(name, json)
      let currentCity = this.currentCity
      let seriesData = []
      let markData = []
      if (currentCity) {//市级数据
        let tempData = this.mapData.find(x => x.name == currentCity)
        console.log("211", name, currentCity)
        seriesData = tempData?.mapArea
        console.log(seriesData)
        
        this.mapData.forEach(x => {
          if (x.markPoints) {
            markData.push(...x.markPoints)
          }
        })
        console.log("markData",markData)
      } else {//全省数据
        console.log(2, this.mapData)
        seriesData = this.mapData
        console.log(seriesData)

      }
      var option = {//市级数据

        //地图配置
        series: [{
          type: 'map',
          map: name,
          zoom: 1.2,
          top: '10%',
          x: 'center',
          label: {
            show: true,
            color: '#770928'
          },
          itemStyle: {
            normal: { // 静态的时候显示的默认样式
              borderWidth: 1, // 边框宽度
              areaColor: '#C0C4CC', // 地图颜色
              borderColor: '#ffffff' // 边框颜色
            },
            emphasis: { // 鼠标移入动态的时候显示的默认样式
              borderWidth: 2, // 边框宽度
              areaColor: '#909399' // 地图颜色
            }
          },

          markPoint: {
            symbol: 'circle',
            symbolSize: 15,
            //文字标签
            label: {
              show: false, //是否显示标签
              position: 'top',//标签的位置
              color: '#ffffff', //文字的颜色
              fontSize: 12 //文字的字体大小
            },
            //标注的样式
            itemStyle: {
              opacity: 0.8,//图形透明度。支持从 0 到 1 的数字，为 0 时不绘制该图形。
              color: '#ffffff'
            },
            silent: true,//图形是否不响应和触发鼠标事件，默认为 false，即响应和触发鼠标事件。
            /**
             * 标注的数据数组。每个数组项是一个对象：{ name: '舒兰市', coord: [126.97170, 44.41223], value: '78', symbolSize: 10, },
             * name 标注名称
             * coord 数据在相应坐标系上的坐标位置 经纬度值
             * value 标注值，可以不设。
             * symbolSize 标记的大小，可以单独设置每个标记的大小
             */
            data: markData
          },
          data: seriesData
          // data: this.currentCityData
        },
        ],
      }
      var cityOption = {//全省数据
        visualMap: {
          min: 0,
          max: 50,
          calculable: false,//s
          orient: 'vertical',
          left: 'right',
          top: 'bottom',
          text: ['50', '0'], // 文本，默认是数值范围

          inRange: {
            color: [
              '#1A00FF', // lightest
              '#CAFF01',
              '#FFB700',
              '#FF5500',
              '#FE0000',
              '#FF1001' // darkest
            ]
          },
          textStyle: {
            color: '#000'
          },

        },
        // graphic: [
        //   {
        //     type: 'text',
        //     z: 100,
        //     left: 'right', // 根据 visualMap 的位置手动调整
        //     top: '70%', // 根据 visualMap 的位置手动调整
        //     style: {
        //       text: 'VisualMap Title', // visualMap 的标题文本
        //       textAlign: 'center',
        //       fill: '#000', // 标题颜色
        //       fontSize: 14
        //     }
        //   }
        // ],
        legend: {
          top: '80%',
          left: 'left',
          orient: 'vertical',
          formatter: '{name}',
          lineHeight: 20
        },

        series: [

          //地图配置
          {
            type: 'map',
            map: name,
            zoom: 1.2,
            top: '10%',
            x: 'center',
            showLegendSymbol: false,
            label: {
              show: true,
              color: '#770928'
            },
            itemStyle: {
              normal: { // 静态的时候显示的默认样式
                borderWidth: 1, // 边框宽度
                areaColor: '#C0C4CC', // 地图颜色
                borderColor: '#ffffff' // 边框颜色
              },
              emphasis: { // 鼠标移入动态的时候显示的默认样式
                borderWidth: 2, // 边框宽度
                areaColor: '#909399' // 地图颜色
              }
            },
            
            // 数据
            data: seriesData
          }

        ],
      }


      this.mapData.forEach(item => {
        const cityName = item.name;
        const center = this.cityCoordinate.find(city => city.name === cityName).center;
        const pieData = item.pieValueData;

        const seriesItem = {
          name: cityName,
          type: 'pie',
          silent: true,
          center: center,
          radius: ['2%', '5%'],
          avoidLabelOverlap: false,
          label: {
            show: false,
            position: 'center'
          },
          visualMap: false,
          labelLine: {
            show: false
          },
          data: pieData,
          itemStyle: {

            normal: {
              opacity: 0.8,

            }
          },

        };

        cityOption.series.push(seriesItem);
      });


      if (this.currentCity) {//
        myChart.setOption(option)

      } else {//最外层的市,只有饼状图

        myChart.setOption(cityOption)
      }
      myChart.on('click', e => {
        console.log(e)
        event.preventDefault();
        event.stopPropagation();
        if (Object.keys(this.province).includes(e.name)) {
          console.log(417, e.name, this.province[e.name])
          this.currentCity = e.name
          this.currentCityPY = this.province[e.name]
        }
        if (Object.keys(this.District).includes(e.name)) {
          console.log(422, e.name, this.District[e.name])
          this.currentArea = e.name
          this.currentAreaPY = this.District[e.name]
        }
        this.darwProvniceMap(e)


      })
      
//       myChart.on('click', 'markPoint', function(params) {
//   console.log('markPoint clicked:', params);
// });
      window.addEventListener("resize", () => {
        myChart.resize()
      })


    },
    // 切换市
    darwProvniceMap (val) {
      if (this.province[val.name]) {
        // console.log("进入263", this.province[val.name])
        // this.currentCity = val.name
        // this.currentCityPY = this.province[val.name]

        let json = require(`@/jsMapJson/${this.province[val.name]}.json`)
        this.drawMap(this.province[val.name], json)
        this.showButton = true
      }
      //进入具体的区
      // if (this.District[val.name]) {

      //   // this.currentArea = val.name
      //   // this.currentAreaPY = this.District[val.name]
      //   let json = require(`@/jsMapJson/${this.currentCityPY}/${this.District[val.name]}.json`)
      //   this.drawMap(this.District[val.name], json)
      //   this.showButton = true
      // }

    },
    // 返回江苏省
    reJiangSuMap () {
      this.currentCity = ""
      this.currentCityPY = ''
      this.currentArea = ''
      this.currentAreaPY = ''
      this.drawMap('jiangsu')
    }
  },
}
</script>

<style scoped>
.rest-button {
  position: absolute;
  right: 60px;
  bottom: 5px;
  z-index: 999;
  border-radius: 6px;
  font-size: 14px;
  border: 1px #770928 solid;
  color: #770928;
  height: 30px;
  line-height: 28px;
  padding: 0 20px;
  overflow: hidden;
  box-sizing: border-box;
  cursor: pointer;
}


#map {

  width: 100%;
  height: 1400px;
  position: relative;
  overflow: hidden;
}

.center-body {
  width: 100%;
}
</style>
