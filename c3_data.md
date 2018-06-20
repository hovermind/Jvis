## Data object
```
data: {
  x : '',
  xFormat: '',
  columns/rows/json,
  keys // for json data
}
```

## JSON
```
data: {
    json: [{
        date: '2014-01-01',
        upload: 200,
        download: 200,
        total: 400
    }, {
        date: '2014-01-02',
        upload: 100,
        download: 300,
        total: 400
    }],
    keys: {
        x: 'date',
        value: ['upload', 'download']
    }
}
```

## Rows
```
data: {
  rows: [
    ['no', 'amount', 'totalPrice'],
    [1, 3, 300],
    [2, 6, 240],
    [3, 8, 290],
    [4, 4, 230]
  ],
  keys: {
    x: 'no',
    value: ['amount', 'totalPrice']
  }
}
```

## Columns
No need for keys
```
data: {
  rows: [
    ['x', 1, 2, 3, 4]
    ['amount', 3, 6, 8 4],
    ['totalPrice', 300, 240, 290, 230],
  ],
  keys: {
    x: 'no',
    value: ['amount', 'totalPrice']
  }
}
```

See: [C3 data details](http://c3js.org/reference.html#data-url)
