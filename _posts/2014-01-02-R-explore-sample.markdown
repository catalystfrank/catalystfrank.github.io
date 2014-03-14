---
title: R的快速数据探索
layout: post
guid: urn:uuid:b87da13a-a4dd-402f-b06a-cef7eeee2d87
tags:
  - 数据探索
  - R
---

### 设置工作路径，请改动

    ```r
    setwd("blahblah/R/EXPLORE/")
    ```

### 加载R Packages

    ```r
    library(ggplot2)
    library(e1071)
    ```

    ```
    ## Loading required package: class
    ```

    ```r
    library(colorspace)
    ```

### 加载数据

#### 传入训练数据文件与参数
*   <em>rdataName</em>：加载文件名
*   <em>rdataDelim</em>：分割符（不支持小数点与单双引号)，默认空格
*   <em>rdataHaveColnames</em>：第一行是否带列名（T/F控制)
*   <em>rdataExtColnames</em>：外部列名手动输入
*   <em>rdataExtColDelim</em>：外部列名分割符，默认逗号

    ```r
    rdataName <- "iris.dat"
    rdataDelim <- " "
    rdataHaveColnames <- T
    rdataExtColnames <- "Sepal.Length,Sepal.Width,Petal.Length,Petal.Width,Species2"
    rdataExtColDelim <- ","
    ```

#### 加载数据

    ```r
    rdataExtColSplit <- strsplit(rdataExtColnames,rdataExtColDelim)[[1]]
    rdataframe<-read.table(rdataName,sep=rdataDelim,header=rdataHaveColnames,
    col.names=rdataExtColSplit, #该行在rdataHaveColnames=T时可选择不传参
    quote = "\"'",
    dec = "."
    )
    ```

#### 将数据顺序打乱

    ```r
    rdataframe <- rdataframe[sample(1:nrow(rdataframe), length(1:nrow(rdataframe))),1:ncol(rdataframe)]
    ```

### 数据探索

#### 数据描述

    ```r
    class_en2zh <- function(s) {
        if (s == "numeric") 
            result <- "数值型" else if (s == "factor") 
            result <- "因子型" else if (s == "character") 
            result <- "字符型" else result <- "未知类型"
    }
    
    df_num_summary <- function(df) {
        datatype <- sapply(sapply(df, class), class_en2zh)
        result <- data.frame(matrix(, 8, 0))
        for (i in 1:length(datatype)) {
            if (datatype[i] == "数值型") {
                c1 <- as.vector(summary(df[, i]))
                skew <- skewness(df[, i])
                kurt <- kurtosis(df[, i])
                result <- cbind(result, c(c1, skew, kurt))
            } else result <- cbind(result, rep(NA, 8))
        }
        result
    }
    
    datatype <- sapply(sapply(rdataframe, class), class_en2zh)  #数值类型
    datamiss <- apply(apply(rdataframe, 2, is.na), 2, sum)  #缺失值数目
    dataunique <- sapply(apply(rdataframe, 2, unique), length)  #不同值数目
    dfsummary <- df_num_summary(rdataframe)  #数值类型变量统计
    names(dfsummary) <- names(datatype)
    
    data_summary_all <- rbind(datamiss, dataunique, dfsummary)
    row.names(data_summary_all) <- c("缺失值计数", "不同值计数", "最小值", "1/4分位点", 
        "中位数", "均值", "3/4分位点", "最大值", "偏度", "峰度")
    names(data_summary_all) <- paste(names(datatype), datatype, sep = "_")
    data_summary_all
    ```

    ```
    ##            Sepal.Length_数值型 Sepal.Width_数值型 Petal.Length_数值型
    ## 缺失值计数              0.0000             0.0000              0.0000
    ## 不同值计数             35.0000            23.0000             43.0000
    ## 最小值                  4.3000             2.0000              1.0000
    ## 1/4分位点               5.1000             2.8000              1.6000
    ## 中位数                  5.8000             3.0000              4.3500
    ## 均值                    5.8400             3.0600              3.7600
    ## 3/4分位点               6.4000             3.3000              5.1000
    ## 最大值                  7.9000             4.4000              6.9000
    ## 偏度                    0.3086             0.3126             -0.2694
    ## 峰度                   -0.6058             0.1387             -1.4169
    ##            Petal.Width_数值型 Species2_因子型
    ## 缺失值计数             0.0000               0
    ## 不同值计数            22.0000               3
    ## 最小值                 0.1000              NA
    ## 1/4分位点              0.3000              NA
    ## 中位数                 1.3000              NA
    ## 均值                   1.2000              NA
    ## 3/4分位点              1.8000              NA
    ## 最大值                 2.5000              NA
    ## 偏度                  -0.1009              NA
    ## 峰度                  -1.3582              NA
    ```


#### 数据可视化快照
*    <em>dataNumIndexString</em>：数值型列序号字符串（从1开始计数），默认逗号分割
*    <em>dataNumIndexSep</em>：数值型变量列序号字符串的分割符
*    <em>dataClassIndexString</em>：分类型列序号字符串（单个）

    ```r
    dataNumIndexString <- "1,2,3,4"
    dataNumIndexSep <- ","
    dataClassIndexString <- "5"
    
    dataNumIndex <- as.integer(strsplit(dataNumIndexString, dataNumIndexSep)[[1]])
    dataClassIndex <- as.integer(dataClassIndexString)
    ```


##### 输出协方差/相关系数矩阵


    ```r
    cov(rdataframe[, dataNumIndex])
    ```

    ```
    ##              Sepal.Length Sepal.Width Petal.Length Petal.Width
    ## Sepal.Length      0.68569    -0.04243       1.2743      0.5163
    ## Sepal.Width      -0.04243     0.18998      -0.3297     -0.1216
    ## Petal.Length      1.27432    -0.32966       3.1163      1.2956
    ## Petal.Width       0.51627    -0.12164       1.2956      0.5810
    ```

    ```r
    cor(rdataframe[, dataNumIndex])
    ```

    ```
    ##              Sepal.Length Sepal.Width Petal.Length Petal.Width
    ## Sepal.Length       1.0000     -0.1176       0.8718      0.8179
    ## Sepal.Width       -0.1176      1.0000      -0.4284     -0.3661
    ## Petal.Length       0.8718     -0.4284       1.0000      0.9629
    ## Petal.Width        0.8179     -0.3661       0.9629      1.0000
    ```


##### 输出散点/单变量直方/相关系数简图


    ```r
    panel.cor <- function(x, y, digits = 2, prefix = "", cex.cor, ...) {
        usr <- par("usr")
        on.exit(par(usr))
        par(usr = c(0, 1, 0, 1))
        r <- abs(cor(x, y, use = "complete.obs"))
        txt <- format(c(r, 0.123456789), digits = digits)[1]
        txt <- paste(prefix, txt, sep = "")
        if (missing(cex.cor)) 
            cex.cor <- 0.8/strwidth(txt)
        text(0.5, 0.5, txt, cex = cex.cor * (1 + r)/2, col = hex(RGB((1 - r)/2, 
            (1 - r)/2, (1 - r)/2), gamma = NULL))
    }
    panel.hist <- function(x, ...) {
        usr <- par("usr")
        on.exit(par(usr))
        par(usr = c(usr[1:2], 0, 1.5))
        h <- hist(x, plot = FALSE)
        breaks <- h$breaks
        nB <- length(breaks)
        y <- h$counts
        y <- y/max(y)
        rect(breaks[-nB], 0, breaks[-1], y, col = "white", ...)
        
    }
    panel.smooth <- function(x, y, ...) {
        usr <- par("usr")
        on.exit(par(usr))
        points(x, y, col = as.integer(factor(rdataframe[, dataClassIndex])))
    }
    pairs(rdataframe[, dataNumIndex], upper.panel = panel.cor, diag.panel = panel.hist, 
        lower.panel = panel.smooth)
    ```

使用R par作图 看着没有GGPLOT2高富帅 啧啧![R_par1.png](\media\pic\2014-01-02\R_par1.png)


### 第一部分、结构之法
* **第一章、字符之魅**
 - [1.1：左旋转字符串](01.01.md)
 - [1.2：字符串是否包含问题](01.02.md)
 - [1.3：带通配符的字符串匹配](01.03.md)
    + [  1.3.1：带通配符的字符串匹配](01.031.md)
    + [  1.3.1：带通配符的字符串匹配](01.031.md)
* **第二章、数组之幻**
 - 2.1：寻找最小的 k 个数 