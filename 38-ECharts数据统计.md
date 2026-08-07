# 数据统计

图表组件

ECharts 组件

https://echarts.apache.org/examples/zh/index.html#chart-type-line

折线图

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title></title>
		<script src="js/echarts.js"></script>
		<script type="text/javascript" src="js/jquery-3.3.1.min.js"></script>
	</head>
	<body>
		<div id="main" style="width: 100%;height:600px;"></div>
		<script type="text/javascript">
			$.ajax({
				url: "http://localhost:8080/line",
				type: "GET",
				dataType: "json", 
				success: function(resp){
					var myChart = echarts.init(document.getElementById('main'));
							
					// 指定图表的配置项和数据
					var option = {
						  xAxis: {
							type: 'category',
							data: resp.names
						  },
						  yAxis: {
							type: 'value'
						  },
						  series: [
							{
							  data: resp.scores,
							  type: 'line'
							}
						  ]
						};
					// 使用刚指定的配置项和数据显示图表。
					myChart.setOption(option);
				}
			});
		</script>
	</body>
</html>
```

```java
@GetMapping("/line")
    public Map line(){
        List<String> names = new ArrayList<>();
        names.add("1月");
        names.add("2月");
        names.add("3月");
        names.add("4月");
        names.add("5月");
        names.add("6月");
        names.add("7月");
        names.add("8月");
        names.add("9月");
        names.add("10月");
        names.add("11月");
        names.add("12月");
        List<Integer> scores = this.newsMapper.list();
        Map<String,List> map = new HashMap<>();
        map.put("names", names);
        map.put("scores", scores);
        return map;
    }
```

```java
@Select({"select coalesce(sum(price),0) from bus_rent where date_format(createtime,'%m') = 1 union all\n" +
        "select coalesce(sum(price),0) from bus_rent where date_format(createtime,'%m') = 2 union all\n" +
        "select coalesce(sum(price),0) from bus_rent where date_format(createtime,'%m') = 3 union all\n" +
        "select coalesce(sum(price),0) from bus_rent where date_format(createtime,'%m') = 4 union all\n" +
        "select coalesce(sum(price),0) from bus_rent where date_format(createtime,'%m') = 5 union all\n" +
        "select coalesce(sum(price),0) from bus_rent where date_format(createtime,'%m') = 6 union all\n" +
        "select coalesce(sum(price),0) from bus_rent where date_format(createtime,'%m') = 7 union all\n" +
        "select coalesce(sum(price),0) from bus_rent where date_format(createtime,'%m') = 8 union all\n" +
        "select coalesce(sum(price),0) from bus_rent where date_format(createtime,'%m') = 9 union all\n" +
        "select coalesce(sum(price),0) from bus_rent where date_format(createtime,'%m') = 10 union all\n" +
        "select coalesce(sum(price),0) from bus_rent where date_format(createtime,'%m') = 11 union all\n" +
        "select coalesce(sum(price),0) from bus_rent where date_format(createtime,'%m') = 12 ;"})
public List<Integer> list();
```

饼图

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title></title>
		<script src="js/echarts.js"></script>
		<script type="text/javascript" src="js/jquery-3.3.1.min.js"></script>
	</head>
	<body>
		<div id="main" style="width: 100%;height:600px;"></div>
		<script type="text/javascript">
			$.ajax({
				url: "http://localhost:8080/pieColor",
				type: "GET",
				dataType: "json", 
				success: function(resp){
					var myChart = echarts.init(document.getElementById('main'));
							
					// 指定图表的配置项和数据
					var option = {
					  backgroundColor: '#2c343c',
					  title: {
						text: '饼状图',
						left: 'center',
						top: 20,
						textStyle: {
						  color: '#ccc'
						}
					  },
					  tooltip: {
						trigger: 'item'
					  },
					  visualMap: {
						show: false,
						min: 1,
						max: 10,
						inRange: {
						  colorLightness: [0, 1]
						}
					  },
					  series: [
						{
						  name: 'Access From',
						  type: 'pie',
						  radius: '55%',
						  center: ['50%', '50%'],
						  data: resp.sort(function (a, b) {
							return a.value - b.value;
						  }),
						  roseType: 'radius',
						  label: {
							color: 'rgba(255, 255, 255, 0.3)'
						  },
						  labelLine: {
							lineStyle: {
							  color: 'rgba(255, 255, 255, 0.3)'
							},
							smooth: 0.2,
							length: 10,
							length2: 20
						  },
						  itemStyle: {
							color: '#c23531',
							shadowBlur: 200,
							shadowColor: 'rgba(0, 0, 0, 0.5)'
						  },
						  animationType: 'scale',
						  animationEasing: 'elasticOut',
						  animationDelay: function (idx) {
							return Math.random() * 200;
						  }
						}
					  ]
					};
					// 使用刚指定的配置项和数据显示图表。
					myChart.setOption(option);
				}
			});
		</script>
	</body>
</html>
```

```java
@GetMapping("/pie")
public List<PieVO> pie(){
    return this.newsMapper.pie();
}
```

```java
@Select({"select cartype name,count(cartype) value from bus_car group by cartype"})
public List<PieVO> pie();
```

柱状图

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title></title>
		<script src="js/echarts.js"></script>
		<script type="text/javascript" src="js/jquery-3.3.1.min.js"></script>
	</head>
	<body>
		<div id="main" style="width: 100%;height:600px;"></div>
		<script type="text/javascript">
			$.ajax({
				url: "http://localhost:8080/bar",
				type: "GET",
				dataType: "json", 
				success: function(resp){
					var myChart = echarts.init(document.getElementById('main'));
							
					// 指定图表的配置项和数据
					var option = {
					  xAxis: {
						type: 'category',
						data: resp.names
					  },
					  yAxis: {
						type: 'value'
					  },
					  series: [
						{
						  data: resp.counts,
						  type: 'bar'
						}
					  ]
					};
					// 使用刚指定的配置项和数据显示图表。
					myChart.setOption(option);
				}
			});
		</script>
	</body>
</html>
```

```java
@GetMapping("/bar")
public Map<String,List> bar(){
    List<BarVO> bar = this.newsMapper.bar();
    List<String> names = new ArrayList<>();
    List<Integer> counts = new ArrayList<>();
    for (BarVO barVO : bar) {
        names.add(barVO.getName());
        counts.add(barVO.getCount());
    }
    Map<String,List> map = new HashMap<>();
    map.put("names", names);
    map.put("counts", counts);
    return map;
}
```

```java
@Select({"select opername name,count(opername) count from bus_rent group by opername"})
public List<BarVO> bar();
```