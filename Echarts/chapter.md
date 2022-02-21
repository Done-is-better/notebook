#### 柱状图 

```javascript
 <script>
        //初始化实例对象
        var myEchart = echarts.init(document.querySelector("#panel"));
        //指定配置项和数据
        var option = {
            /*
                color:['','red']  --设置颜色
                
            ];*/
            //图标标题
            title: {
                text: "myEchart demo 01"
            },
            //图标提示框组件
            tooltip: {
				
            },
            legend: {
                data: ["销量"] //series里有name时可以省略
            },
            xAxis: {
                data: ["column1", "column2", "column3", "column4", "column5"]
            },
            yAxis: {},
            series: {
                name: "销量",
                type: "bar",
                data: [12, 23, 41, 6, 111]
            }
            
        };
        myEchart.setOption(option);
		//图标自使用屏幕大小
		window.addEventListener("resize",function(){
            myEchart.resize();
        })
    </script>
```

![image-20211201171916278](C:\Users\Simon Kerola\AppData\Roaming\Typora\typora-user-images\image-20211201171916278.png)

> **Echarts基础配置**
>
> **<a>series</a>**  系列列表，通过type决定图标类型
>
> <a>**xAxis**</a> X轴
>
> ​		<a>boundaryGap</a>  坐标两边留白策略true，这时刻度线只是作为分隔符，标签和数据点都会在两个刻度的带(band) 中间。
>
> ​		<a>type</a> = 
>
> ​				*'value'* :数值轴，适用于连续数据
>
> ​				*'category'*: 类目轴，适用于离散的类目数据，为该类型时必须通过data设置的类目数据。
>
> ​				*'time'*: 时间轴，适用于连续的时序数据，与数值轴相比带有时间的格式化，在刻度计算上不同
>
> ​				*'log'*: 对数轴。适用于对数数据
>
> <a>**yAxis**</a> Y轴
>
> <a>**grid**</a> 直角坐标系内绘图网格
>
> ```javascript
> grid:{
>     left:'9%',
>     right:'4%',
>     bottom:'3%',
>     //是否显示刻度标签
>     containLabel:true
> }
> ```
>
> 
>
> <a>**title**</a>  标题组件
>
> <a>**tooltip**</a> 提示框组件
>
> ​			<a>item</a>  /   <a>axis</a>   /  <a>none</a> 
>
> <a>**legend**</a>  图例组件
>
> <a>**toolbox**</a>  工具箱组件
>
> ```javascript
> toolbox:{
>     feature:{
>         saveAsImage:{}
>     }
> }
> ```
>
> 

```javascript
//轴文字标签样式
axisLabel:{
    color:"rgba(121,121,121,.6)",
        fontSize:"12"
}
//轴线条样式
axisLine:{
    lineStyle:{
        color:"red"
    }
},
//轴分割线样式
splitLine:{
    lineStyle:{
        color:"rgba(12,12,12,.3)"
    }
}
```

#### 曲线图

```javascript
(function() {
    var myEchart = echarts.init(document.querySelector("#chart1"));
    var option = {
        color: ["#00ff12", "#f03", "#123", "#0f3e12"],
        tooltip: {
            trigger: "axis"
        },
        legend: {
            //data: ["邮箱营销", "联盟广告", "视频广告", "直接访问"]
            textStyle: {
                color: "#4c9bfd"
            },
            right: "10%" //距离右边10%
        },
        grid: {
            left: "3%",
            right: "4%",
            bottom: "3%",
            containLabel: true,
            //显示边框
            show: true,
            borderColor: "#012f4a"
        },
        /* toolbox: {
            feature: {
                saveAsImage: {}
            }
        }, */
        xAxis: {
            type: "category",
            boundaryGap: false, //去除轴内间距
            data: ["周一", "周二", "周三", "周四", "周五", "周六", "周日"],
            axisTick: {
                show: false //不显示刻度线
            },
            axisLabel: {
                color: "#4c9bfd"
            },
            axisLine: {
                show: false
            }
        },
        yAxis: {
            type: "value",
            boundaryGap: false,
            axisTick: {
                show: false
            },
            axisLabel: {
                color: "#4c9bfd"
            },
            axisLine: {
                show: false
            },
            splitLine: {
                lineStyle: {
                    color: "#012f4a"
                }
            }
        },
        series: [{
            name: "邮件营销",
            type: "line",
            //圆滑曲线
            smooth: true,
            stack: "总量",
            data: [120, 132, 101, 134, 90, 230, 210]
        }, {
            name: "联盟广告",
            type: "line",
            smooth: true,
            stack: "总量",
            data: [410, 232, 301, 134, 230, 100, 110]
        }, {
            name: "视频广告",
            type: "line",
            smooth: true,
            stack: "总量",
            data: [160, 332, 201, 134, 290, 280, 250]
        }, {
            name: "直接访问",
            type: "line",
            smooth: true,
            stack: "总量",
            data: [190, 232, 201, 94, 120, 130, 260]
        }]
    }
    myEchart.setOption(option);
    /* window.addEventListener("resize", function() {
        myEchart.resize();
    }, 200); */
    setTimeout(function() {
        window.onresize = function() {
            myEchart.resize();
        }
    });
})();
```

 

#### 折线图

```javascript
series:[{
    {
            name: "直接登录",
            data: [123, 23, 33, 42, 123, 5, 142],
            type: "line",
            stack: "总量",
            smooth: true,
            //修改当先线条样式
            lineStyle: {
                color: "blue",
                width: 2
            },
            areaStyle: {
                color: new echarts.graphic.LinearGradient(
                    0,
                    0,
                    0,
                    1, [{
                            offset: 0,
                            //渐变色起始颜色
                            color: "rgba(1,1,213,.9)"
                        },
                        {
                            offset: 0.8,
                            //结束颜色
                            color: "rgba(111,132,31,.1)"
                        }
                    ], false
                ),
                shadowColor: "rgba(1,1,1,.2)"
            },
            //设置拐点、大小及显示
            symbol: "circle",
            symbolSize: 4,
            showSymbol: false,
            //设置拐点颜色、边框
            itemStyle: {
                color: "yello",
                borderColor: "rgba(13,45,31,.5)",
                borderWidth: 12
            }
        }
}]
```

#### 饼状图

```javascript
option = {
    tooltip:{
        trigger:"item",
        formatter:"{a}<br/>{b}:{c}({d}%)"
    },
    legend:{
        orient:"vertical",
        left:10,
        data:["直接访问",...]
    },
    series:[
              {
              name:"访问来源",
              type:"pie",
              radius:["50%","70%"],
        	  avoidLabelOverlap:false,
        	  label:{
                  show:false,
                  position:"center"
              },
        		emphasis:{
                    label:{
                        show:true,
                        fontSize:"30",
                        fontWeight:"bold"
                    }
                },
        labelLine:{
            show:false
        },
        data:[
            {value:335,name:"直接访问"},
            {value:232,name:"邮件营销"},
            {value:232,name:"联盟广告"},
            ...
        ]
              }
             ]
}
```



#### 南丁格尔图

```javascript
(function() {
    var myEchart = echarts.init(document.querySelector("#chart3"));
    var option = {
        title: {
            text: "南丁格尔玫瑰图",
            subtext: "副标题",
            left: "center"
        },
        tooltip: {
            trigger: "item",
            /** 
             * a:
             * b:
             * c:
             * d:
             */
            formatter: "{a}<br/>:{c}({d}%)"
        },
        legend: {
            left: "center",
            top: "bottom",
            // data: ["rose1", "rose2", "rose3", "rose4", "rose5", "rose6", "rose7"]
        },
        toolbox: {
            show: true,
            feature: {
                //
                mark: { show: true },
                //数据视图
                dataView: {
                    show: true,
                    readOnly: false
                },
                magicType: {
                    show: true,
                    type: ["pie", "funnel"]
                },
                //恢复
                restore: { show: true },
                saveAsImage: { show: true }
            }
        },
        series: [{
                name: "半径模式",
                type: "pie",
                //内外半径
                radius: [20, 110],
                center: ["25%", "50%"],
                //半径模式
                roseType: "radius",
                label: {
                    show: false
                },
                labelLine: {
                    length: 12,
                    length2: 20
                },
                emphasis: {
                    label: {
                        show: true
                    }
                },
                data: [
                    { value: 10, name: "rose1" },
                    { value: 5, name: "rose2" },
                    { value: 15, name: "rose3" },
                    { value: 25, name: "rose4" },
                    { value: 20, name: "rose5" },
                    { value: 25, name: "rose6" },
                    { value: 30, name: "rose7" }
                ]
            },
            {
                name: "面积模式",
                type: "pie",
                radius: [30, 110],
                center: ["75%", "50%"],
                //面积模式
                roseType: "area",
                label: { fontSize: 12 },
                labelLine: {
                    length: 12,
                    length2: 24
                },
                data: [
                    { value: 10, name: "rose1" },
                    { value: 5, name: "rose2" },
                    { value: 15, name: "rose3" },
                    { value: 25, name: "rose4" },
                    { value: 20, name: "rose5" },
                    { value: 25, name: "rose6" },
                    { value: 30, name: "rose7" }
                ]
            }
        ]
    };
    myEchart.setOption(option);
})();
```

#### 雷达图

```javascript
 var myEchart = echarts.init(document.querySelector("#chart5"));
    var option = {
        title: {
            text: "radar"
        },
        radar: {
            indicator: [{
                    name: "feature1",
                    max: 4200
                }, {
                    name: "feature2",
                    max: 2400
                },
                {
                    name: "feature3",
                    max: 2400
                },
                {
                    name: "feature4",
                    max: 3000
                }, {
                    name: "feature5",
                    max: 4000
                }
            ]
        },
        series: {
            type: "radar",
            data: [{
                name: "goods1",
                value: [4000, 1030, 2400, 2000, 1000]
            }, {
                name: "goods2",
                value: [4200, 2400, 1200, 1800, 2300]
            }]
        }
    };
    myEchart.setOption(option);
```

#### 小结



![image-20211202192148618](C:\Users\Simon Kerola\AppData\Roaming\Typora\typora-user-images\image-20211202192148618.png)

![image-20211202192349428](C:\Users\Simon Kerola\AppData\Roaming\Typora\typora-user-images\image-20211202192349428.png)

#### 主题的使用

> Echarts 内置了两套主题：
>
> ```javascript
> var e = echarts(dom,"light")
> var e = echarts(dom,"dark")
> ```
>
> 

#### 柱状图补充

```javascript
(function() {
    var myEchart = echarts.init(document.querySelector("#chart1"), "light");
    var option = {
        tooltip: {
            //触发类型，或写成type
            trigger: "item", //可选项：item/axis
            //触发时机
            triggerOn: "click", //可选项： mouseover/click
            //格式化
            //formatter: "{b} 的成绩是 {c}" /**  */
            formatter: function(args) {
                // console.log(args);
                return args.name + " 的成绩是： " + args.data;
            }
        },
        toolbox: {
            feature: {
                saveAsImage: {}, //保存图片
                restore: {}, //恢复
                dataView: {}, //数据视图
                dataZoom: {}, //区域缩放
                magicType: { //类型切换
                    type: ["bar", "line"]
                }
            }
        },
        xAxis: {
            type: "category",
            data: ["chandler", "emily", "zoe", "tom", "joey"]
        },
        yAxis: {
            type: "value"
        },
        series: [{
            name: "语文",
            type: "bar",
            data: [87, 99, 94, 67, 88, 95],
            //显示最高分，最低分
            markPoint: {
                data: [
                    { name: "最高分", type: "max" },
                    { name: "最低分", type: "min" }
                ]
            },
            //显示平均值
            markLine: {
                data: [
                    { name: "平均值", type: "average" }
                ]
            },
            //显示具体信息
            label: {
                show: true,
                //文字旋转显示
                rotate: 60,
                //文字位置
                position: "top"
            },
            barWidth: "30%"
        }]
    };
    myEchart.setOption(option);
})();
```

##### 通用配置

> <a>**tooltip**</a> 提示框组件，用于配置鼠标滑过或点击图标时的显示框
>
> <a>trigger</a> 触发类型
>
> <a>triggerOn</a> 触发时机
>
> <a>formatter</a> 格式化

#### 折线图补充

```javascript
(function() {
    var myEchart = echarts.init(document.querySelector("#chart2"), "light");
    var xArray = ["1月", "2月", "3月", "4月", "5月", "6月", "7月", "8月", "9月", "10月", "11月", "12月"];
    var yArray = [3000, 2800, 2790, 2810, 2810, 2803, 2805, 2830, 2810, 2830, 2840, 2780];
    var option = {
        tooltip: {
            trigger: "item",
        },
        xAxis: {
            type: "category",
            data: xArray,
            boundaryGap: false //第一个类目轴重合y轴
        },
        yAxis: {
            type: "value",
            scale: true //y轴不从0开始
        },
        series: [{
                data: yArray,
                type: "line",
                name: "销售量",
                smooth: true,
                lineStyle: {
                    color: "rgba(0,0,0,.8)",
                    type: "dotted" //dotted solid dashed
                },
                areaStyle: {
                    color: new echarts.graphic.LinearGradient(
                        0, 0, 0, 1, [{
                            offset: 0,
                            color: "rgba(1,1,213,.9)"
                        }, {
                            offset: 0.8,
                            color: "rgba(111,132,23,.1)"
                        }], false
                    )
                },
                markPoint: {
                    data: [
                        { name: "最大值", type: "max" },
                        { name: "最低值", type: "min" }
                    ]
                },
                markLine: {
                    data: [
                        { name: "平均值", type: "average" }
                    ]
                },
                markArea: {
                    data: [
                        [{ xAxis: "1月" }, { xAxis: "2月" }],
                        [{ xAxis: "6月" }, { xAxis: "8月" }]
                    ]
                }
            }

        ]
    };
    myEchart.setOption(option);
})();
```

#### 散点图

```javascript
(function() {
    var myEchart = echarts.init(document.querySelector("#chart3"), "light");
    var option = {
        xAxis: {
            type: "value",
            scale: true //非零刻度显示
        },
        yAxis: {
            type: "value",
            scale: true
        },
        series: [{
            type: "scatter",
            data: [
                [161.2, 51.6],
                [167.5, 59.0],
                [159.5, 49.2],
                [157.0, 63.0],
                [155.8, 53.6],
                [170.0, 59.0],
                [159.1, 47.6],
                [166.0, 69.8],
                [176.2, 66.8],
                [160.2, 75.2],
                [172.5, 55.2],
                [170.9, 54.2],
                [172.9, 62.5],
                [153.4, 42.0],
                [160.0, 50.0],
                [147.2, 49.8],
                [168.2, 49.2],
                [175.0, 73.2],
                [157.0, 47.8],
                [167.6, 68.8],
                [159.5, 50.6],
                [175.0, 82.5],
                [166.8, 57.2],
                [176.5, 87.8],
                [170.2, 72.8],
                [174.0, 54.5],
                [173.0, 59.8],
                [179.9, 67.3],
                [170.5, 67.8],
                [160.0, 47.0],
                [154.4, 46.2],
                [162.0, 55.0],
                [176.5, 83.0],
                [160.0, 54.4],
                [152.0, 45.8],
                [162.1, 53.6],
                [170.0, 73.2],
                [160.2, 52.1],
                [161.3, 67.9],
                [166.4, 56.6],
                [168.9, 62.3],
                [163.8, 58.5],
                [167.6, 54.5],
                [160.0, 50.2],
                [161.3, 60.3],
                [167.6, 58.3],
                [165.1, 56.2],
                [160.0, 50.2],
                [170.0, 72.9],
                [157.5, 59.8],
                [167.6, 61.0],
                [160.7, 69.1],
                [163.2, 55.9],
                [152.4, 46.5],
                [157.5, 54.3],
                [168.3, 54.8],
                [180.3, 60.7],
                [165.5, 60.0],
                [165.0, 62.0],
                [164.5, 60.3],
                [156.0, 52.7],
                [160.0, 74.3],
                [163.0, 62.0],
                [165.7, 73.1],
                [161.0, 80.0],
                [162.0, 54.7],
                [166.0, 53.2],
                [174.0, 75.7],
                [172.7, 61.1],
                [167.6, 55.7],
                [151.1, 48.7],
                [164.5, 52.3],
                [163.5, 50.0],
                [152.0, 59.3],
                [169.0, 62.5],
                [164.0, 55.7],
                [161.2, 54.8],
                [155.0, 45.9],
                [170.0, 70.6],
                [176.2, 67.2],
                [170.0, 69.4],
                [162.5, 58.2],
                [170.3, 64.8],
                [164.1, 71.6],
                [169.5, 52.8],
                [163.2, 59.8],
                [154.5, 49.0],
                [159.8, 50.0],
                [173.2, 69.2],
                [170.0, 55.9],
                [161.4, 63.4],
                [169.0, 58.2],
                [166.2, 58.6],
                [159.4, 45.7],
                [162.5, 52.2],
                [159.0, 48.6],
                [162.8, 57.8],
                [159.0, 55.6],
                [179.8, 66.8],
                [162.9, 59.4],
                [161.0, 53.6],
                [151.1, 73.2],
                [168.2, 53.4],
                [168.9, 69.0],
                [173.2, 58.4],
                [171.8, 56.2],
                [178.0, 70.6],
                [164.3, 59.8],
                [163.0, 72.0],
                [168.5, 65.2],
                [166.8, 56.6],
                [172.7, 105.2],
                [163.5, 51.8],
                [169.4, 63.4],
                [167.8, 59.0],
                [159.5, 47.6],
                [167.6, 63.0],
                [161.2, 55.2],
                [160.0, 45.0],
                [163.2, 54.0],
                [162.2, 50.2],
                [161.3, 60.2],
                [149.5, 44.8],
                [157.5, 58.8],
                [163.2, 56.4],
                [172.7, 62.0],
                [155.0, 49.2],
                [156.5, 67.2],
                [164.0, 53.8],
                [160.9, 54.4],
                [162.8, 58.0],
                [167.0, 59.8],
                [160.0, 54.8],
                [160.0, 43.2],
                [168.9, 60.5],
                [158.2, 46.4],
                [156.0, 64.4],
                [160.0, 48.8],
                [167.1, 62.2],
                [158.0, 55.5],
                [167.6, 57.8],
                [156.0, 54.6],
                [162.1, 59.2],
                [173.4, 52.7],
                [159.8, 53.2],
                [170.5, 64.5],
                [159.2, 51.8],
                [157.5, 56.0],
                [161.3, 63.6],
                [162.6, 63.2],
                [160.0, 59.5],
                [168.9, 56.8],
                [165.1, 64.1],
                [162.6, 50.0],
                [165.1, 72.3],
                [166.4, 55.0],
                [160.0, 55.9],
                [152.4, 60.4],
                [170.2, 69.1],
                [162.6, 84.5],
                [170.2, 55.9],
                [158.8, 55.5],
                [172.7, 69.5],
                [167.6, 76.4],
                [162.6, 61.4],
                [167.6, 65.9],
                [156.2, 58.6],
                [175.2, 66.8],
                [172.1, 56.6],
                [162.6, 58.6],
                [160.0, 55.9],
                [165.1, 59.1],
                [182.9, 81.8],
                [166.4, 70.7],
                [165.1, 56.8],
                [177.8, 60.0],
                [165.1, 58.2],
                [175.3, 72.7],
                [154.9, 54.1],
                [158.8, 49.1],
                [172.7, 75.9],
                [168.9, 55.0],
                [161.3, 57.3],
                [167.6, 55.0],
                [165.1, 65.5],
                [175.3, 65.5],
                [157.5, 48.6],
                [163.8, 58.6],
                [167.6, 63.6],
                [165.1, 55.2],
                [165.1, 62.7],
                [168.9, 56.6],
                [162.6, 53.9],
                [164.5, 63.2],
                [176.5, 73.6],
                [168.9, 62.0],
                [175.3, 63.6],
                [159.4, 53.2],
                [160.0, 53.4],
                [170.2, 55.0],
                [162.6, 70.5],
                [167.6, 54.5],
                [162.6, 54.5],
                [160.7, 55.9],
                [160.0, 59.0],
                [157.5, 63.6],
                [162.6, 54.5],
                [152.4, 47.3],
                [170.2, 67.7],
                [165.1, 80.9],
                [172.7, 70.5],
                [165.1, 60.9],
                [170.2, 63.6],
                [170.2, 54.5],
                [170.2, 59.1],
                [161.3, 70.5],
                [167.6, 52.7],
                [167.6, 62.7],
                [165.1, 86.3],
                [162.6, 66.4],
                [152.4, 67.3],
                [168.9, 63.0],
                [170.2, 73.6],
                [175.2, 62.3],
                [175.2, 57.7],
                [160.0, 55.4],
                [165.1, 104.1],
                [174.0, 55.5],
                [170.2, 77.3],
                [160.0, 80.5],
                [167.6, 64.5],
                [167.6, 72.3],
                [167.6, 61.4],
                [154.9, 58.2],
                [162.6, 81.8],
                [175.3, 63.6],
                [171.4, 53.4],
                [157.5, 54.5],
                [165.1, 53.6],
                [160.0, 60.0],
                [174.0, 73.6],
                [162.6, 61.4],
                [174.0, 55.5],
                [162.6, 63.6],
                [161.3, 60.9],
                [156.2, 60.0],
                [149.9, 46.8],
                [169.5, 57.3],
                [160.0, 64.1],
                [175.3, 63.6],
                [169.5, 67.3],
                [160.0, 75.5],
                [172.7, 68.2],
                [162.6, 61.4],
                [157.5, 76.8],
                [176.5, 71.8],
                [164.4, 55.5],
                [160.7, 48.6],
                [174.0, 66.4],
                [163.8, 67.3]
            ]
        }]
    };
    myEchart.setOption(option);
```

#### 饼图补充

```javascript
(function() {
    var myEchart = echarts.init(document.querySelector("#chart4"), "light");
    var option = {
        series: [{
            type: "pie",
            data: [
                { value: 335, name: "直接访问" },
                { value: 232, name: "邮件营销" },
                { value: 232, name: "联盟广告" },
                { value: 123, name: "bilibili" }
            ],
            label: {
                show: true,
                formatter: function(arg) {
                    console.log(arg);
                    return arg.name + " : " + arg.data.value + "\n" + arg.percent + "%";
                }
            },
            //radius: "50%", //参照weight和height中值较小的的一般进行%
            //radius: ["50%", "75%"],
            roseType: "radius", //南丁格尔图
            //选中效果
            selectedMode: "single", //偏离效果，multiple选中多个
            selectedOffset: 30 //偏离距离 
        }]
    };
    myEchart.setOption(option);
})();
```

#### 渐变色补充

```javascript
//区域渐变
(function() {
    var myEchart = echarts.init(document.querySelector("#chart5"), "light");
    var option = {
        xAxis: {
            type: "category",
            data: ["item1", "item2", "item3", "item4"]
        },
        yAxis: {
            type: "value"
        },
        series: [{
            name: "name",
            data: [34, 533, 12, 89],
            type: "line",
            areaStyle: {
                color: new echarts.graphic.LinearGradient(
                    0, 0, 0, 1, [
                        { offset: 0, color: "red" },
                        { offset: 1, color: "blue" }
                    ], false
                )
            }
        }]
    };
    myEchart.setOption(option);
})();
```

```javascript
//线性渐变
(function() {
    var myEchart = echarts.init(document.querySelector("#chart6"), "dark");
    var option = {
        xAxis: {
            data: ["item1", "item2", "item3", "item4"],
            type: "category"
        },
        yAxis: {
            type: "value"
        },
        series: [{
            name: "apple",
            data: [12, 32, 14, 9],
            type: "bar",
            itemStyle: {
                //线性渐变
                color: {
                    type: "linear",
                    x: 0,
                    y: 0,
                    x2: 0,
                    y2: 1,
                    colorStops: [
                        { offset: 0, color: "green" },
                        { offset: 0.5, color: "blue" },
                        { offset: 1, color: "red" }
                    ]
                }
            }
        }]
    };
    myEchart.setOption(option);
})();
```

```javascript
//径向渐变
(function() {
    var myEchart = echarts.init(document.querySelector("#chart6"), "dark");
    var option = {
        xAxis: {
            data: ["item1", "item2", "item3", "item4"],
            type: "category"
        },
        yAxis: {
            type: "value"
        },
        series: [{
            name: "apple",
            data: [12, 32, 14, 9],
            type: "bar",
            itemStyle: {
                color: {
                    x: 0.5,
                    y: 0.5,
                    r: 0.5,
                    colorStops: [
                        { offset: 0, color: "red" },
                        { offset: 0.5, color: "green" },
                        { offset: 1, color: "blue" }
                    ]
                }
            }
        }]
    };
    myEchart.setOption(option);
})();
```

#### 样式

> **直接样式**
>
> <a>itemStyle,textStyle,lineStyle,areaStyle,label</a> 
>
> **高亮样式**
>
> 在<a>emphasis</a> 中包裹<a>itemStyle,textStyle,lineStyle,areaStyle,label</a> 

#### 加载动画

```javascript
var myChart = ...
myChart.showLoading() //显示加载动画
$.get("data/test.json",function(args){
    myChart.hideLoading(); //隐藏加载动画
})
```

