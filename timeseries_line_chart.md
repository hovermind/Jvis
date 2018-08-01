## Folder structure
```
-my_chart.html
-css
  - c3.css
  - my_chart.css
-js
  -require_config.js
  -my_chart.js
  -lib
    -d3.js
    -c3.js
    -jquery.js
    -require.js
  -module
    -timeseries_chart.js
    -ajax_helper.js
```
#### Downloads:
* [jQuery](https://jquery.com/download/)
* [D3](https://github.com/d3/d3/releases)
* [C3 (C3.js + C3.css)](https://github.com/c3js/c3/releases)
* [require.js](https://github.com/requirejs/requirejs/releases)


#### Create:
* `my_chart.css` (if you need your own styling)
* `ajax_helper.js` (AMD module)
* `timeseries_chart.js` (AMD module)
* `my_chart.js` (here we will use AMD modules)


## API
API running on localhost => (Spring boot) URI : `http://localhost:8080/my_chart`

## Html `my_chart.html`
```
<!doctype html>
<html>
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

  <title>C3 Line Chart</title>
  
  <link rel="stylesheet" href="./css/c3.css">
  <link rel="stylesheet" href="./css/my_chart.css">
  
  <script src="js/lib/require.js"></script>
  <script src="js/require_config.js"></script>
  
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

## RequireJS config (main entry point) `require_config.js`
```
requirejs.config({
	baseUri: contextPath + '/js', // load all module from here
	paths: { // modules located in sub-dir of baseUri (baseUri will be prepended to path)
		"d3" : ["https://cdnjs.cloudflare.com/ajax/libs/d3/4.13.0/d3.min.js", "lib/d3.min"],
		"c3" : ["https://cdnjs.cloudflare.com/ajax/libs/c3/0.6.2/c3.min.js", "lib/c3.min"],
		'jquery' : ["https://code.jquery.com/jquery-3.3.1.min.js", "lib/jquery"]
	}
});
```

## Custom modules

#### `timeseries_chart.js`
```
define(["d3", "c3"], function(d3, c3){
	
	'use strict';

	// constants
	const INCOMING_TIME_FORMAT = '%Q'; // '%Q' => timestamp in milliseconds
	// Note: for type: 'timeseries' (X_AXIS_SETTING) xFormat: INCOMING_TIME_FORMAT might be ignored

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

	let ts = {
		
		Chart: '',
		
		drawTSChartWithJsonData: ({
			DIV_ID,
			JSON_DATA,
			VALUE_KEYS,
			X_AXIS = 'datetime',
			LINE_TYPE = 'spline',
			Y_AXIS_LABEL = ''
		}) => {
			
			// required fields
			if( !(DIV_ID && JSON_DATA && VALUE_KEYS) ){
				console.log("one or more required fields absent");
				return;
			}
			
			if(Y_AXIS_LABEL){ // default is set in Y_AXIS_SETTING
				Y_AXIS_SETTING.label.text = Y_AXIS_LABEL;
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
			
			ts.Chart = chart;
		}
	};
	
	return ts;
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

	let ajaxHelper = {
		
		FetchData: ({method = 'GET', uri, payload = '', dataType = 'json', callback}) => {
			
			let $call;
			
			if(method == 'GET'){
				
				$call = $.ajax(uri, {
					type : method,
					contentType : 'application/json', // send as
					dataType : dataType, // return as
				});
				
			} else {
				
				$call = $.ajax(uri, {
					type : method,
					contentType : 'application/json', // send as
					dataType : dataType, // return as
					data : payload
				});
			}

			$call.done(data => {
				//console.log(data);
				
				response.isError = false;
				response.message = 'Data fetched!';
				response.data = data;
				
				callback(response);
				
			}).fail(error => {
				//console.log(error);
				//console.log(error.statusText);
				
				response.isError = true;
				response.message = error.statusText;
				response.data = '';

				callback(response);
			});
		}
	};
	
	return ajaxHelper;
});
```

## Using custom modules `my_chart.js`

#### Using API (ajax call)
```
require(['module/ajax_helper', 'module/timeseries_chart' ], function(AjaxHelper, TimeSeriesChart) {

	'use strict';

	let chartUri = 'http://localhost:8080' + "/my_chart";

	// balance chart
	AjaxHelper.FetchData({
		method : 'GET',
		uri : `${chartUri}/balance`,
		callback : (response) => {

			if (response.isError) {
				console.log('Failed to get data');
				console.log('Cause: ' + response.message);
				return;
			}

			TimeSeriesChart.drawTSChartWithJsonData({
				DIV_ID : '#balance_chart',
				JSON_DATA : response.data.dataList,
				VALUE_KEYS : response.data.keys,
				X_AXIS : 'datetime',
				Y_AXIS_LABEL : 'Currency Value'
			});
		}
	});
	
	// count chart
	AjaxHelper.FetchData({
		method : 'GET',
		uri : `${chartUri}/count`,
		callback : (response) => {

			if (response.isError) {
				console.log('Failed to get data');
				console.log('Cause: ' + response.message);
				return;
			}

			TimeSeriesChart.drawTSChartWithJsonData({
				DIV_ID : '#count_chart',
				JSON_DATA : response.data.dataList,
				VALUE_KEYS : response.data.keys,
				X_AXIS : 'datetime',
				Y_AXIS_LABEL : 'Count'
			});
		}
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
