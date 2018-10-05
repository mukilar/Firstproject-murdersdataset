---
title: "Report on Gun Murders"
author: "MukilaR"
date: "5 October 2018"
output:

  
  html_document: default
---

```{r setup, include=FALSE,echo=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```

## Introduction

This is a report on 2010 gun murder rates obtained from FBI reports. The original data was obtained from [this Wikipedia page](https://en.wikipedia.org/wiki/Murder_in_the_United_States_by_state).

This data has 51 rows and 6 columns. 


```{r loading-libs, message=FALSE}
library(tidyverse)
```
```{r}
load("rda/murders.rda")

```
##Basic scatterplot
First, we create a scatterplot for total murders versus population size.
```{r scatterplot,echo=FALSE}
murders %>% ggplot(aes(x = population, y =total )) +
  geom_point()+ggtitle("Gun murder data")
```

##Adding labels
To the above scatter plot, we add the state abbreviations as labels and use different colors to represent the different regions.
```{r,echo=FALSE}
murders %>% ggplot(aes(population, total, label = abb)) +
  geom_label(aes(color=region))+ggtitle("Gun murder data")

```

Now we are going to change the axes to log scales to account for the fact that the population distribution is skewed. 
```{r,echo=FALSE}
p <- murders %>% ggplot(aes(population, total, label = abb, color = region)) + geom_label()
p+scale_x_log10() +scale_y_log10()+ggtitle("Gun murder data")

```

##Creating a chart for different regions
Let us compare the gun homicide rates across regions(North Central, Northeast, South, West) of the US.
```{r,echo=FALSE}
murders %>% mutate(rate = total/population*100000) %>%mutate(region = reorder(region,rate))%>%
  group_by(region) %>%
  summarize(avg = mean(rate),med=median(rate)) %>%
  mutate(region = factor(region)) %>%
  ggplot(aes(region, avg )) +
  geom_bar(stat="identity",aes(fill=region)) +xlab("Region")+
  ylab("Murder Rate Average")+ggtitle("Gun murder data by region")+coord_flip()
```

From the above graph, we can see that the Western and Northeastern region have the lowest gun murder rates. So, is it a wise decision to move to the West? To investigate this further, we make a boxplot of murder rates by region, showing all points.

```{r,echo=FALSE}
murders %>% mutate(rate = total/population*100000) %>%
  mutate(region=reorder(region, rate, FUN=median)) %>%
  ggplot(aes(region, rate)) +
  geom_boxplot(aes(fill=region)) +
  geom_point()+ggtitle("Boxplot of murder rates by region")+coord_flip()

```
The boxplot clearly shows that the western region has the lowest median of murder rate compared to other regions in the US.

## Murder rate by state 

We note the large state to state variability by generating a barplot showing the murder rate by state:

```{r fig.height = 8, fig.width = 7, echo=FALSE}
murders <- murders %>% mutate(region = factor(region), rate = total / population * 10^5)
murders %>% mutate(abb = reorder(abb, rate)) %>%
  ggplot(aes(abb, rate)) +
  geom_bar(width = 0.5, stat = "identity", aes(fill=region)) +ggtitle("Murder rate by state")+ylab("Rate")+
  xlab("State")+coord_flip()
```
 

