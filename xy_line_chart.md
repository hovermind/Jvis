## Folder structure
```
-index.html
-css
-js
  -main.js
  -require.js
  -chart.js
  -lib
    -d3.js
    -c3.js
  -module
    -xy_chart.js
```
Local API (Spring boot) URI : `http://localhost:8080/chart/ts`

## Html `index.html`
```
<!doctype html>
<html>
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

  <title>C3 Line Chart</title>
  
  <link rel="stylesheet" href="./css/line_chart.css">
  
  <script data-main="js/main.js" src="js/require.js"></script>
  
  <style>
	h2{ text-align: center;}
  </style>
  
</head>

<body>

	<hr />
	<h2>User Count Chart</h2>
	<hr />
	<div id="user_count_chart"></div>
	
	<script src="js/chart.js"></script>
</body>

</html>

```

## RequireJS config (main entry point) `main.js`
```
requirejs.config({
	//baseUri: 'js', // load all module from here
	paths: { // modules located in sub-dir of baseUri (baseUri will be prepended to path)
		//"d3" : ["https://cdnjs.cloudflare.com/ajax/libs/d3/4.13.0/d3.min.js", "lib/d3.min"],
		//"c3" : ["https://cdnjs.cloudflare.com/ajax/libs/c3/0.6.2/c3.min.js", "lib/c3.min"],
		d3 : ["lib/d3"],
		c3 : ["lib/c3"]
	}
});
```

## Custom module `xy_chart.js`
```
define(["d3", "c3"], function(d3, c3){
	
	'use strict';

	// constants
	const INCOMING_TIME_FORMAT = '%Q'; // timestamp in milliseconds

	// axis setting
	let X_AXIS_SETTING = {
	  tick: {
		fit: true,
		rotate: 45,
		format: d3.format("d")
	  },
	  label: { text: 'Value', position: 'outer-center' },
	  //min: xMin,
	  //max: xMax,
	  //padding: { left: 0, right: 100 },
	  //height: 50
	};

	let Y_AXIS_SETTING = {
	  label: { text: 'Value', position: 'outer-middle' },
	  min: 0,
	  padding: { bottom: 0, top: 50 }
	};

	let LEGEND_SETTING = {
	  show: true,
	  position: 'inset',
	  inset: {
		anchor: 'top-right',
		x: 10,
		y: -10,
		step: 1
	  }
	};

	let GRID_SETTING = { x: { show: true }, y: { show: true } };

	let obj = {
		
		Chart: '',
		
		drawTSChartWithJsonData: function(chartInfo) {
			
			let DIV_ID = chartInfo.DIV_ID;
			let JSON_DATA = chartInfo.JSON_DATA;
			let VALUE_KEYS = chartInfo.VALUE_KEYS;
			
			let LINE_TYPE = chartInfo.LINE_TYPE;
			if(!LINE_TYPE){
				LINE_TYPE = 'line'; // default
			}
			
			let X_AXIS = chartInfo.X_AXIS;
			if(!X_AXIS){
				X_AXIS = 'X Value'; // default
			}
			if(chartInfo.X_AXIS_LABEL){ // default is set in X_AXIS_SETTING
				X_AXIS_SETTING.label.text = chartInfo.X_AXIS_LABEL; 
			}
			if(chartInfo.X_AXIS_TICK_FORMAT){ // default is set in X_AXIS_SETTING
				X_AXIS_SETTING.tick.format = d3.format(chartInfo.X_AXIS_TICK_FORMAT); 
			}
			if(chartInfo.Y_AXIS_LABEL){ // default is set in Y_AXIS_SETTING
				Y_AXIS_SETTING.label.text = chartInfo.Y_AXIS_LABEL;
			}
			
			// adjust x axis tick marks
			X_AXIS_SETTING.tick.count = JSON_DATA.length * 2;
			
			let chart = c3.generate({
				bindto: DIV_ID,
				data: {
				  type: LINE_TYPE,
				  x: X_AXIS,
				  json: JSON_DATA,
				  keys: { x: X_AXIS, value: VALUE_KEYS }
				},
				axis: { x: X_AXIS_SETTING, y: Y_AXIS_SETTING },
				legend: LEGEND_SETTING,
				grid: GRID_SETTING,
				padding: { right: 20 }
			});
			
			this.Chart = chart;
		}
	};
	
	return obj;

});
```

## Using custom modules `chart.js`
```
require(["module/xy_chart"], function(XYChart){

	let userChartData = [{
	  amount: 15000,
	  count: 25
	}, {
	  amount: 25000,
	  count: 15
	}, {
	  amount: 5000,
	  count: 150
	}, {
	  amount: 35000,
	  count: 40
	}];

	let keys = ["amount"];

	XYChart.drawTSChartWithJsonData({
		DIV_ID: '#user_count_chart', 
		JSON_DATA: userChartData, 
		VALUE_KEYS: keys,
		LINE_TYPE: 'line',
		X_AXIS: 'count',
		X_AXIS_LABEL: 'User Count',
		X_AXIS_TICK_FORMAT: 'd',
		Y_AXIS_LABEL: 'Amount'
	});
});
```
