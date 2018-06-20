## Html
```
<div id="myLineChart"></div>
```

## JS
```
'use strict';

// constants
const X_AXIS = 'x';
const INCOMING_TIME_FORMAT = '%Y%m%d';


// axis setting
var xMin = new Date('2013-01-01');
var xMax = new Date('2013-06-17');
var yMin = 0;
var yMax = 600;

var xAxisSetting = {
            type: 'timeseries',
            tick: {
                format: '%b, %Y'
            },
            label: {
              text: 'Time',
              position: 'outer-center'
        	},
            min: xMin,
            max: xMax,
            padding: {left: 0, right: 0}
        };
		
var yAxisSetting = {
            label: {
              text: 'Currency Value',
              position: 'outer-middle'
        	},
            min: yMin,
            padding: {bottom: 0, top: 100}
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
    
var gridSetting = {
  x: {show: true},
  y: {show: true}
};
        
// chart
function drawLineChart(divId, columnData){

	var chart = c3.generate({
		bindto: divId,
		data: {
			type: 'spline',
			x: X_AXIS,
			xFormat: INCOMING_TIME_FORMAT,
			columns: columnData
		},
		axis: {
			x: xAxisSetting,
			y: yAxisSetting
		},
		legend: legendSetting,
    grid: gridSetting
	});
}
// data
var timeData = [X_AXIS, '20130101', '20130202', '20130303', '20130404', '20130505', '20130606'];
var jpyData = ['JPY', 30, 200, 100, 400, 150, 250];
var usdData = ['USD', 130, 340, 200, 500, 250, 350];
drawLineChart('#myLineChart', [timeData, jpyData, usdData]);
```
#### CSS
```
/*-- Chart --*/


.c3 svg {
  font: 10px sans-serif;
}
.c3 path, .c3 line {
  fill: none;
  stroke: #000;
}
.c3 text {
  -webkit-user-select: none;
     -moz-user-select: none;
          user-select: none;
}

.c3-legend-item-tile,
.c3-xgrid-focus,
.c3-ygrid,
.c3-event-rect,
.c3-bars path {
  shape-rendering: crispEdges;
}

.c3-chart-arc path {
  stroke: #fff;

}
.c3-chart-arc text {
  fill: #fff;
  font-size: 13px;
}

/*-- Axis --*/

.c3-axis-x .tick {
}
.c3-axis-x-label {
    color: red;
}

.c3-axis-y .tick {
}
.c3-axis-y-label {

}

.c3-axis-y2 .tick {
}
.c3-axis-y2-label {
}

/*-- Grid --*/

.c3-grid line {
  stroke: #aaa;
}
.c3-grid text {
  fill: #aaa;
}
.c3-xgrid, .c3-ygrid {
  stroke-dasharray: 3 3;
}
.c3-xgrid-focus {
}

/*-- Text on Chart --*/

.c3-text {
}

.c3-text.c3-empty {
  fill: #808080;
  font-size: 2em;
}

/*-- Line --*/

.c3-line {
  stroke-width: 1px;
}
/*-- Point --*/

.c3-circle._expanded_ {
  stroke-width: 1px;
  stroke: white;
}
.c3-selected-circle {
  fill: white;
  stroke-width: 2px;
}

/*-- Bar --*/

.c3-bar {
  stroke-width: 0;
}
.c3-bar._expanded_ {
  fill-opacity: 0.75;
}

/*-- Arc --*/

.c3-chart-arcs-title {
  font-size: 1.3em;
}

/*-- Focus --*/

.c3-target.c3-focused {
  opacity: 1;
}
.c3-target.c3-focused path.c3-line, .c3-target.c3-focused path.c3-step {
  stroke-width: 2px;
}
.c3-target.c3-defocused {
  opacity: 0.3 !important;
}


/*-- Region --*/

.c3-region {
  fill: steelblue;
  fill-opacity: .1;
}

/*-- Brush --*/

.c3-brush .extent {
  fill-opacity: .1;
}

/*-- Select - Drag --*/

.c3-dragarea {
}

/*-- Legend --*/

.c3-legend-item {
  font-size: 12px;
}
.c3-legend-item-hidden {
  opacity: 0.15;
}

.c3-legend-background {
  opacity: 0.75;
  fill: white;
  stroke: lightgray;
  stroke-width: 1
}

/*-- Tooltip --*/

.c3-tooltip-container {
  z-index: 10;
}
.c3-tooltip {
  border-collapse:collapse;
  border-spacing:0;
  background-color:#fff;
  empty-cells:show;
  -webkit-box-shadow: 7px 7px 12px -9px rgb(119,119,119);
     -moz-box-shadow: 7px 7px 12px -9px rgb(119,119,119);
          box-shadow: 7px 7px 12px -9px rgb(119,119,119);
  opacity: 0.9;
}
.c3-tooltip tr {
  border:1px solid #CCC;
}
.c3-tooltip th {
  background-color: #aaa;
  font-size:14px;
  padding:2px 5px;
  text-align:left;
  color:#FFF;
}
.c3-tooltip td {
  font-size:13px;
  padding: 3px 6px;
  background-color:#fff;
  border-left:1px dotted #999;
}
.c3-tooltip td > span {
  display: inline-block;
  width: 10px;
  height: 10px;
  margin-right: 6px;
}
.c3-tooltip td.value{
  text-align: right;
}

.c3-area {
  stroke-width: 0;
  opacity: 0.2;
}

.c3-chart-arcs .c3-chart-arcs-background {
  fill: #e0e0e0;
  stroke: none;
}
.c3-chart-arcs .c3-chart-arcs-gauge-unit {
  fill: #000;
  font-size: 16px;
}
.c3-chart-arcs .c3-chart-arcs-gauge-max {
  fill: #777;
}
.c3-chart-arcs .c3-chart-arcs-gauge-min {
  fill: #777;
}

.c3-chart-arc .c3-gauge-value {
  fill: #000;
/*  font-size: 28px !important;*/
}
```
