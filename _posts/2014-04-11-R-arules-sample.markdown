---
title: R：关联规则
layout: post
guid: urn:uuid:b87da13a-a4dd-402f-b06a-cef7eeee2d88
tags:
  - 关联规则
  - R
  - Apriori
  - Arules
  - FP-Growth
---

### 设置工作路径

    setwd("blahblah/R/Arules/")

### 加载R Packages

    library(ggplot2)
    library(arules)
    library(arulesViz)
    library(pmml)
    

### 加载数据

#### 参数定义
*   <em>rdataName</em>：加载文件名
*   <em>rdataDelim</em>：分割符（不支持小数点与单双引号)，默认逗号
*   <em>InfSupport</em>：支持度最小值
*   <em>InfConfd</em>：置信度最小值
*   <em>NRules</em>：输出规则数
*   <em>NVizRules1/2</em>：输出可视化规则数
 
#### 传入参数

    rdataName <- "Groceries.sparse"
    rdataDelim <- ","
    InfSupport <- "0.01"
    InfConfd <- "0.02"
    NRules <- "10"
    NVizRules1 <- "120"
    NVizRules2 <- "25"

#### 加载数据与参数

    G <- read.transactions(rdataName, sep = rdataDelim)
    IS <- as.numeric(InfSupport)
    IC <- as.numeric(InfConfd)
    NR <- as.integer(NRules)
    NVR1 <- as.integer(NVizRules1)
    NVR2 <- as.integer(NVizRules2)

#### 计算参数，得到规则

    rules <- apriori(G, parameter = list(support = IS, confidence = IC))
    

    ## 
    ## parameter specification:
    ##  confidence minval smax arem  aval originalSupport support minlen maxlen
    ##        0.02    0.1    1 none FALSE            TRUE    0.01      1     10
    ##  target   ext
    ##   rules FALSE
    ## 
    ## algorithmic control:
    ##  filter tree heap memopt load sort verbose
    ##     0.1 TRUE TRUE  FALSE TRUE    2    TRUE
    ## 
    ## apriori - find association rules with the apriori algorithm
    ## version 4.21 (2004.05.09)        (c) 1996-2004   Christian Borgelt
    ## set item appearances ...[0 item(s)] done [0.00s].
    ## set transactions ...[169 item(s), 9835 transaction(s)] done [0.01s].
    ## sorting and recoding items ... [88 item(s)] done [0.00s].
    ## creating transaction tree ... done [0.01s].
    ## checking subsets of size 1 2 3 4 done [0.01s].
    ## writing ... [581 rule(s)] done [0.00s].
    ## creating S4 object  ... done [0.00s].


### 结果展现

#### 规则列表

    ## 按支持度查看前N条规则
    LR <- length(rules)
    inspect(sort(rules, by = "support")[1:min(NR, LR)])  
    
    ##    lhs    rhs                support confidence lift
    ## 1  {}  => {whole milk}       0.25552    0.25552    1
    ## 2  {}  => {other vegetables} 0.19349    0.19349    1
    ## 3  {}  => {rolls/buns}       0.18393    0.18393    1
    ## 4  {}  => {soda}             0.17438    0.17438    1
    ## 5  {}  => {yogurt}           0.13950    0.13950    1
    ## 6  {}  => {bottled water}    0.11052    0.11052    1
    ## 7  {}  => {root vegetables}  0.10900    0.10900    1
    ## 8  {}  => {tropical fruit}   0.10493    0.10493    1
    ## 9  {}  => {shopping bags}    0.09853    0.09853    1
    ## 10 {}  => {sausage}          0.09395    0.09395    1
    
    ##按置信度查看前N条规则
    inspect(sort(rules, by = "confidence")[1:min(NR, LR)])

    ##    lhs                     rhs                support confidence  lift
    ## 1  {citrus fruit,                                                     
    ##     root vegetables}    => {other vegetables} 0.01037     0.5862 3.030
    ## 2  {root vegetables,                                                  
    ##     tropical fruit}     => {other vegetables} 0.01230     0.5845 3.021
    ## 3  {curd,                                                             
    ##     yogurt}             => {whole milk}       0.01007     0.5824 2.279
    ## 4  {butter,                                                           
    ##     other vegetables}   => {whole milk}       0.01149     0.5736 2.245
    ## 5  {root vegetables,                                                  
    ##     tropical fruit}     => {whole milk}       0.01200     0.5700 2.231
    ## 6  {root vegetables,                                                  
    ##     yogurt}             => {whole milk}       0.01454     0.5630 2.203
    ## 7  {domestic eggs,                                                    
    ##     other vegetables}   => {whole milk}       0.01230     0.5525 2.162
    ## 8  {whipped/sour cream,                                               
    ##     yogurt}             => {whole milk}       0.01088     0.5245 2.053
    ## 9  {rolls/buns,                                                       
    ##     root vegetables}    => {whole milk}       0.01271     0.5230 2.047
    ## 10 {other vegetables,                                                 
    ##     pip fruit}          => {whole milk}       0.01352     0.5175 2.025


#### 规则-置信度分布，按不同阶规则着色

    smallrules1 <- sort(rules, by = "confidence")[1:min(NVR1, LR)]
    smallrules2 <- sort(rules, by = "confidence")[1:min(NVR2, LR)]
    plot(rules, shading = "order", control = list(main = "Two-key plot"))

![pic51.png](\media\pic\2014-04-11\pic51.png)

#### 左右手规则统计

    plot(smallrules1, method = "grouped")

![pic52.png](\media\pic\2014-04-11\pic52.png)

#### 因果图

    plot(smallrules2, method = "graph")

![pic53.png](\media\pic\2014-04-11\pic53.png)

### 结果存储

    write.PMML(rules, file = "rule.xml")
    
### 文件下载

[Groceries.sparse](\media\pic\2014-04-11\Groceries.sparse)