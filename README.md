長庚大學 大數據分析方法 作業六
================

組員姓名：江昭輝
----------------

分析議題背景
------------

分析空氣品質的差異是否會影響新北市民對YouBike的使用量

分析動機
--------

常看到一些空氣品質的報導,想確認一般大眾是否會在意

使用資料
--------

1.「新北市YouBike從105年5月26日~106年3月31日的使用量」(XinBei\_YouBike\_UseData.csv) 2.「環境空氣品質的各種資料\_從104年8月到106年1月」(Environmental\_pollution\_InFo.csv)

載入使用資料們

``` r
library(readr)
```

    ## Warning: package 'readr' was built under R version 3.3.3

``` r
XinBei_YouBike_UseData <- read_csv("C:/XinBei_YouBike_UseData.csv", 
   col_names = FALSE,skip = 1)
```

    ## Parsed with column specification:
    ## cols(
    ##   .default = col_integer(),
    ##   X1 = col_character(),
    ##   X3 = col_character(),
    ##   X36 = col_double(),
    ##   X37 = col_double()
    ## )

    ## See spec(...) for full column specifications.

``` r
Environmental_pollution_InFo <- read_csv("C:/Environmental_pollution_InFo.csv")
```

    ## Parsed with column specification:
    ## cols(
    ##   ItemName = col_character(),
    ##   Category = col_character(),
    ##   Year = col_integer(),
    ##   Month = col_integer(),
    ##   ItemValue = col_character(),
    ##   ItemUnit = col_character()
    ## )

資料處理與清洗
--------------

``` r
library(dplyr)
```

    ## Warning: package 'dplyr' was built under R version 3.3.3

    ## 
    ## Attaching package: 'dplyr'

    ## The following objects are masked from 'package:stats':
    ## 
    ##     filter, lag

    ## The following objects are masked from 'package:base':
    ## 
    ##     intersect, setdiff, setequal, union

``` r
library(data.table)
```

    ## -------------------------------------------------------------------------

    ## data.table + dplyr code now lives in dtplyr.
    ## Please library(dtplyr)!

    ## -------------------------------------------------------------------------

    ## 
    ## Attaching package: 'data.table'

    ## The following objects are masked from 'package:dplyr':
    ## 
    ##     between, first, last

``` r
#從「環境空氣品質的各種資料」中抽取所需要的欄位:空氣品質保護
Needed_Environ_Info<-subset(Environmental_pollution_InFo,Category=="空氣品質保護")
#把數值欄位中的"－"取代為0
Needed_Environ_Info$ItemValue<-gsub("－",0,Needed_Environ_Info$ItemValue)
```

探索式資料分析
--------------

``` r
#從「新北市YouBike從105年5月26日~106年3月31日的使用量」找出各租借點的出現次數
DT1<-data.table(XinBei_YouBike_UseData)
DT_XinBei_YouBike_UseData<-DT1[,.N,by=X3]
#把ItemValue轉換成數值型態
DT2<-data.table(Needed_Environ_Info)
DT2$ItemValue<-as.numeric(DT2$ItemValue)
#統計出各種空氣品質數據的月平均值
Monthly_Mean<-group_by(DT2,Month,ItemName)%>%
summarise(MonthlyMean=mean(ItemValue))
#找出各種空氣品質數據的最大值
MaxPolValue<-DT2[,.(MaxValue=max(ItemValue)),by=ItemName]
```

期末專題分析規劃
----------------

希望能找出空氣品質越糟糕YouBike租借量會越少的關係
