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
    -timeseries_chart.js
```

## Html
```
<!doctype html>
<html>
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

  <title>C3 Timeseries Line Chart</title>
  
  <link rel="stylesheet" href="./css/line_chart.css">
  
  <script data-main="js/main.js" src="js/require.js"></script>
  
  <style>
	h2{ text-align: center;}
  </style>
  
</head>

<body>
	<hr />
	<h2>Wallet Balance</h2>
	<hr />
	<div id="wallet_balance_chart"></div>

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

## Custom module `timeseries_chart.js`
```
define(["d3", "c3"], function(d3, c3){
	
	'use strict';

	// constants
	const INCOMING_TIME_FORMAT = '%s';

	// axis setting
	let xMin = 1514770562000;
	let xMax = 1526780162000;
	let yMin = 0;

	let xAxisSetting = {
	  type: 'timeseries',
	  tick: {
		fit: true,
		format: "%Y-%m-%d",
		count: 8,
		rotate: 45
	  },
	  label: {
		text: 'Date',
		position: 'outer-center'
	  },
	  //min: xMin,
	  //max: xMax,
	  padding: {
		//left: 0,
		//right: 100
	  },
	  //height: 50
	};

	let yAxisSetting = {
	  label: {
		text: 'Currency Value',
		position: 'outer-middle'
	  },
	  min: yMin,
	  padding: {
		bottom: 0,
		top: 50
	  }
	};

	// legend setting
	let legendSetting = {
	  show: true,
	  position: 'inset',
	  inset: {
		anchor: 'top-right',
		x: 10,
		y: -10,
		step: 1
	  }
	};

	// grid setting
	let gridSetting = {
	  x: {
		show: true
	  },
	  y: {
		show: true
	  }
	};

	let obj = {
		
		X_AXIS : 'timestamp', // default
		LINE_TYPE : 'spline', // default

		drawChartWithJsonData: function(divId, jsonData, keysData) {
			
			var chart = c3.generate({
			bindto: divId,
			data: {
			  type: this.LINE_TYPE,
			  x: this.X_AXIS,
			  xFormat: INCOMING_TIME_FORMAT,
			  json: jsonData,
			  keys: {
				  x: this.X_AXIS,
				  value: keysData
			  }
			},
			axis: {
			  x: xAxisSetting,
			  y: yAxisSetting
			},
			legend: legendSetting,
			grid: gridSetting,
			padding: {
			  right: 20
			}
			});
		}
	};
	
	return obj;

});
```

## Using custom module `chart.js`
```
require(["module/timeseries_chart"], function(TimeSeriesChart){
	
	var jsonData = [{
	  timestamp: 1516586421000,
	  JPY: 30,
	  USD: 130
	}, {
	  timestamp: 1519264821000,
	  JPY: 200,
	  USD: 340
	}, {
	  timestamp: 1521684021000,
	  JPY: 100,
	  USD: 200
	}, {
	  timestamp: 1524362421000,
	  JPY: 400,
	  USD: 500
	}];
	
	
	TimeSeriesChart.LINE_TYPE = 'line';
	TimeSeriesChart.drawChartWithJsonData('#wallet_balance_chart', jsonData, ['JPY', 'USD']);
});
```
