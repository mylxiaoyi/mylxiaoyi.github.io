title: 将XLSX转换为CSV
date: 2015-11-30 15:02:03
categories:
- R
tags:
- r
---

最近需要使用`R`来简单地处理一些数据。不过拿到手里的数据文件是Excel格式的（XLSX），而在我的`R`环境中读取却一直没有成功，于是想到将其转换为CSV格式再进行读取处理。

将XLSX文件转换为CSV文件需要使用`xlsx2csv`小程序，在[Debian](http://debian.org)上可以方便地使用`apt-get`来安装：

``` bash
apt-get install xlsx2csv
```

另外需要一提的就是Debian中还有一个`xls2csv`小程序，正如其名所示，这个小程序可以将Excel 2007以前的文件转换为CSV格式，但却无法处理XLXS文件。

如果有多个文件需要进行转换操作，可以使用下面的脚本进行批量转换：

``` bash
for file in $(ls *.xlsx)
do
  echo "processing $file ..."
  csv_name=$(basename $file .xlsx)".csv"
  xlsx2csv $file $csv_name
done
```

得到CSV文件后，就可以使用`R`来进行所需要的处理了。
