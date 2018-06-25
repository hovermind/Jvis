## Html
```
<div id="myLineChart"></div>
```

## JS
```
'use strict';

// constants
const X_AXIS = 'timestamp';
const INCOMING_TIME_FORMAT = '%s';

// axis setting
var xMin = 1514770562000;
var xMax = 1526780162000;
var yMin = 0;

var xAxisSetting = {
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
  height: 70
};

var yAxisSetting = {
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
var legendSetting = {
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
var gridSetting = {
  x: {
    show: true
  },
  y: {
    show: true
  }
};


function drawLineChartWithJsonData(divId, jsonData, keysData) {

  var chart = c3.generate({
    bindto: divId,
    data: {
      type: 'spline',
      x: X_AXIS,
      xFormat: INCOMING_TIME_FORMAT,
      json: jsonData,
      keys: keysData
    },
    axis: {
      x: xAxisSetting,
      y: yAxisSetting
    },
    legend: legendSetting,
    grid: gridSetting,
    padding: {
      right: 10
    }
  });
}

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

/* var jsonData = [{
  timestamp: 1516586421,
  JPY: 30,
  USD: 130
}, {
  timestamp: 1519264821,
  JPY: 200,
  USD: 340
}, {
  timestamp: 1521684021,
  JPY: 100,
  USD: 200
}, {
  timestamp: 1524362421,
  JPY: 400,
  USD: 500
}]; */

var keysData = {
  x: X_AXIS,
  value: ['JPY', 'USD']
};


drawLineChartWithJsonData('#myLineChart', jsonData, keysData);

```
