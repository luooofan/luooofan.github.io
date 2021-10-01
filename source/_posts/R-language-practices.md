---
title: R language practices
date: 2021-10-01 15:48:22
categories: ProgrammingLanguage 
tags: R
description: 帮同学写两个小程序，简单学了一下R语言，记录一下代码
keywords:
cover: false
---

# R语言实践

帮同学写两个小程序，简单学了一下R语言，记录一下代码

## 市级方言多样性指数热力图

Data：

多样性数据：其中的diver1作为指标

![image-20211001161120048](http://img.luooofan.site/20211001-162623-image-20211001161120048.png)

地图数据来自：[horizon2020](http://horizon2020.wqdian.cn/)

其他数据站：[gadm](https://gadm.org/)

Code：

```R
library(sf)
library(xlsx)
library(ggplot2)
setwd('/path/to/R')

diversity=read.xlsx('diversity.xlsx',sheetIndex=1)
china_zh_map=st_read('horizon2020/分市.shp')
citynum=length(diversity$city)
cityname=china_zh_map$行政区划_c
# subset(china_zh_map,FIRST_行政=="安徽省")$行政区划_c
for(idx in 1:citynum){
	city = diversity$city[idx]
	# some special cases
	if(grepl("思茅地区",city))
		city="普洱市"
	if(grepl("三门峽",city))
		city="三门峡市"
	if(grepl("莱芜",city))
		city="济南市"
	if(grepl("巢湖",city))
		city="合肥市"
	if(!city %in% cityname){
		# city is not found in cityname
		if(grepl("地区",city))
			city=sub("地区","",city)
		if(grepl("市",city))
			city=sub("市","",city)
		loc = grep(city,cityname) # return a num or logical(0)
		if(length(loc)){
			# the string prefixed with city is found in cityname
			diversity$city[idx]=cityname[loc]
		}
		else{
			# not found		
			print(city)
		}
	}
}
colnames(china_zh_map)[1]='city'
map_with_data=merge(china_zh_map,diversity,all=TRUE)
map_with_data$diver2[is.na(map_with_data$diver2)]=0
map_with_data$diver1[is.na(map_with_data$diver1)]=0
ggplot()+
	geom_sf(data=map_with_data,aes(fill=diver1),col="grey85")+
	scale_fill_gradient(low="white",high="steelblue") +
     	coord_sf() +
	theme(              
          panel.grid = element_blank(),
          panel.background = element_blank(),
          axis.text = element_blank(),
          axis.ticks = element_blank(),
          axis.title = element_blank()
      )
```

Result：

![image-20211001161020523](http://img.luooofan.site/20211001-162624-image-20211001161020523.png)



## 地级市到拉萨市的距离

有一份城市经纬度的excel表格，求表格中各地级市到拉萨市的距离

Data：

![image-20211001155727891](http://img.luooofan.site/20211001-162625-image-20211001155727891.png)

Code：

```R
library(xlsx)
library(geosphere)
setwd("/path/to/R")

data=read.xlsx('城市经纬度.xlsx',sheetIndex=1)
data$longitude=as.numeric(data[,6])
data$latitude=as.numeric(data[,7])
point = data[which(data$城市=="拉萨市"),8:9]
distance <- distGeo(data[,8:9], point)
data$distance=distance
write.xlsx(x=data,file='result.xlsx',sheetName='result',row.names=FALSE)
```

Result：

![image-20211001155929019](http://img.luooofan.site/20211001-162625-image-20211001155929019.png)





## References

- [GADM: 全球行政区划数据库](https://docs.gmt-china.org/6.1/dataset/gadm/)
- [数据地图系列8|R语言版数据地图（下）](https://cloud.tencent.com/developer/article/1090397)
- [中国省市县级地图绘制geojson和topojson的方法-适用于PowerBI等多种可视化工具作图](https://zhuanlan.zhihu.com/p/126172145)
- [R 函数笔记 | grep()函数与R语言中的正则表达式](https://www.jianshu.com/p/11bbfa8e98c5)
- [数据合并-join函数](http://xukuang.github.io/blog/2016/01/join-function/)
- [logical(0) in if statement](https://stackoverflow.com/questions/48626193/logical0-in-if-statement)
- [关于r：如何重命名data.frame中的单个列？](https://www.codenong.com/7531868/)

