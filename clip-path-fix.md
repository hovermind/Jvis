## Point (dot) Clipping Fix
```
var chartLayer = d3.select(chart.element).select("." + c3.chart.internal.fn.CLASS.chart);
var chartLayerParentNode = chartLayer.node().parentNode;
var chartLayerNode = chartLayer.remove();
chartLayerParentNode.appendChild(chartLayerNode.node());

chartLayer.attr("clip-path", null);
```
