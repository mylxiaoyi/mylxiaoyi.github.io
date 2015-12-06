title: 利用R将CSV转换为JSON
date: 2015-11-30 16:13:10
categories:
- R
tags:
- r
- json
- csv
---

帮师兄处理数据，不仅需要提取指定的列项数据，而且要保存为JSON格式，以方便其后续在[D3](http://d3.org)中处理。在网上搜索了一下，在`R`中保存为JSON格式数据，可以使用[jsonlite](https://cran.r-project.org/package=jsonlite)包。

安装`jsonlite`包：

``` r
install.packages('jsonlite')
```

将数据转换为JSON格式：

``` r
library(jsonlite)
data <- read.csv("my_data.csv")
json_data <- toJSON(data, pretty = TRUE)
```

将转换后的数据保存为文件：

``` r
writeLines(json_data, "my_data.json")
```
