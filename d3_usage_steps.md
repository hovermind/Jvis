## Usage Steps 
```
  // create selection
  var selection = d3.select('body').selectAll('div');

  // create binding between selection and data
  var binding = selection.data([50, 100, 150]);

  // update existing nodes
  binding
    .style('width', function(d) { return d + 'px'; });

  // create nodes for new data
  binding.enter()
    .append('div')
    .style('width', function(d) { return d + 'px'; });

  // remove nodes for discarded data
  binding.exit()
    .remove();
```
