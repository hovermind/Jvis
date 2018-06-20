## xFormat
For time series chart, timestamp/date is plottet on x axis. `xFormat` indicates the format of (incoming) data for x axis.    

C3 uses `xFormat`(D3 date format) to parse data for x axis.
```
data: {
    x: 'timestamp',
    xFormat: '%Q', // %Q => D3 date format for unix time
    columns: [
        ['timestamp', 'unix_time...', 'unix_time...', 'unix_time...', 'unix_time...', 'unix_time...', 'unix_time...']
	      ['JPY', 30, 200, 100, 400, 150, 250],
	      ['USD', 130, 340, 200, 500, 250, 350]
    ]
}
```
See: [D3 date format details](https://github.com/d3/d3-time-format)
