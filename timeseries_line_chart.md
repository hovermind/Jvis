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
    -jquery.js
  -module
    -timeseries_chart.js
    -ajax_helper.js
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
	<h2>Balance Chart</h2>
	<hr />
	<div id="balance_chart"></div>
	
	<hr />
	<h2>Count Chart</h2>
	<hr />
	<div id="count_chart"></div>
	
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
		c3 : ["lib/c3"],
		'jquery' : ["lib/jquery"]
	}
});
```

## Custom modules

#### `timeseries_chart.js`
```
define(["d3", "c3"], function(d3, c3){
	
	'use strict';

	// constants
	const INCOMING_TIME_FORMAT = '%Q'; // timestamp in milliseconds

	// axis setting
	let X_AXIS_SETTING = {
	  type: 'timeseries',
	  tick: {
		fit: true,
		format: "%Y-%m-%d",
		rotate: 45
	  },
	  label: { text: 'Datetime', position: 'outer-center' },
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
				X_AXIS = 'datetime'; // default
			}
			
			if(chartInfo.Y_AXIS_LABEL){ // default is set in Y_AXIS_SETTING
				Y_AXIS_SETTING.label.text = chartInfo.Y_AXIS_LABEL;
			}
			
			// adjust x axis tick marks
			X_AXIS_SETTING.tick.count = JSON_DATA.length * 2;
			
			let chart = c3.generate({
				bindto: DIV_ID,
				data: {
				  xFormat: INCOMING_TIME_FORMAT,
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

#### `ajax_helper.js`
```
define(["jquery"], function($){
	
	'use strict';
	
	let response = {
		isError : false,
		message : '',
		data : ''
	};

	let obj = {
		
		FetchData: function(uri, callback){
			
			// submit form
			$.get(uri, {
				
				contentType : 'application/json', // send as
				dataType : 'json', // return as
				
			}).done(function(data) {

				//console.log(data);
				
				response.isError = false;
				response.message = 'Data fetched!';
				response.data = data;
				
				callback(response);
				
			}).fail(function(error) {

				//console.log(error);
				//console.log(error.statusText);
				
				response.isError = true;
				response.message = error.statusText;
				response.data = '';

				callback(response);

			});
		}
	}
	
	return obj;
});
```

## Using custom modules `chart.js`

#### Using API (ajax call)
```
require(["module/ajax_helper"], function(AjaxHelper){
	
	'use strict';
	
	require(["module/timeseries_chart"], function(TimeSeriesChart){
		
		AjaxHelper.FetchData("http://localhost:8080/chart/ts", function(response){
			
			if(response.isError){
				console.log('Failed to get data');
				console.log('Cause: ' + response.message);
				return;
			}

			TimeSeriesChart.drawTSChartWithJsonData({
				DIV_ID: '#balance_chart', 
				JSON_DATA: response.data.dataList, 
				VALUE_KEYS: response.data.keys,
				X_AXIS: 'datetime',
				LINE_TYPE: 'line',
				Y_AXIS_LABEL: 'Currency Value'
			});
		});
	
});
```

#### Using dummy data
```
require(["module/timeseries_chart"], function(TimeSeriesChart){

	'use strict';

	let countChartData = [{
	  datetime: '1516586421000',
	  count: 25
	}, {
	  datetime: '1519264821000',
	  count: 15
	}, {
	  datetime: '1521684021000',
	  count: 150
	}, {
	  datetime: '1524362421000',
	  count: 40
	}];

	let keys = ["count"];

	TimeSeriesChart.drawTSChartWithJsonData({
		DIV_ID: '#count_chart', 
		JSON_DATA: countChartData, 
		VALUE_KEYS: keys,
		X_AXIS: 'datetime',
		LINE_TYPE: 'spline',
		Y_AXIS_LABEL: 'Count'
	});
});
```
