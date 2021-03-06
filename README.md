---
title: "floods Bloeschl"
author: "Kmicha71"
date: "5 5 2021"
output:
  html_document: 
    keep_md: true
  pdf_document: default
---



## 500yr floodn series

Derrived from 

Blöschl, G., Kiss, A., Viglione, A. et al. Current European flood-rich period exceptional compared with past 500 years. Nature 583, 560–566 (2020). https://doi.org/10.1038/s41586-020-2478-3



```r
allFloods <- read.csv("https://raw.githubusercontent.com/tuwhydro/500yrfloods/master/i_f.csv", sep=",")
allSites <- read.csv("https://raw.githubusercontent.com/tuwhydro/500yrfloods/master/sites.csv", sep=",")
allSites <- subset(allSites, allSites$lon4 == "O")
allSites <- subset(allSites, (allSites$lat1 > 45) & (allSites$lat1 < 55))
allSites <- subset(allSites, (allSites$lon1 > 5) & (allSites$lon1 < 15))
names(allSites)[names(allSites) == 'code'] <- 'Code'

allFloods$Season[allFloods$Season == ""] <- "undated"
allFloods <- subset(allFloods, allFloods$Index!="no_info")
allFloods$fli = 0
allFloods$fli[allFloods$Index == "no_flood"] <- 0
allFloods$fli[allFloods$Index == "prob_no_flood"] <- 0.5
allFloods$fli[allFloods$Index == "class1"] <- 2
allFloods$fli[allFloods$Index == "class2"] <- 3
allFloods$fli[allFloods$Index == "class3"] <- 4

someFloods <- merge(allFloods,allSites, by=c("Code"))

monthlyFloods <- setNames(data.frame(t(c(0,0,0))),  c("year", "month", "fli"))
for(i in 1:nrow(someFloods)) {
  if((someFloods$Season[i] == 'spring')  | (someFloods$Season[i] == 'undated'))   {
     monthlyFloods[nrow(monthlyFloods) + 1,] = c(someFloods$Year[i],3, someFloods$fli[i])
     monthlyFloods[nrow(monthlyFloods) + 1,] = c(someFloods$Year[i],4, someFloods$fli[i])  
     monthlyFloods[nrow(monthlyFloods) + 1,] = c(someFloods$Year[i],5, someFloods$fli[i])     
  }
  if((someFloods$Season[i] == 'summer')  | (someFloods$Season[i] == 'undated'))   {
     monthlyFloods[nrow(monthlyFloods) + 1,] = c(someFloods$Year[i],6, someFloods$fli[i])
     monthlyFloods[nrow(monthlyFloods) + 1,] = c(someFloods$Year[i],7, someFloods$fli[i])
     monthlyFloods[nrow(monthlyFloods) + 1,] = c(someFloods$Year[i],8, someFloods$fli[i])     
  }
  if((someFloods$Season[i] == 'autumn')  | (someFloods$Season[i] == 'undated'))   {
     monthlyFloods[nrow(monthlyFloods) + 1,] = c(someFloods$Year[i],9, someFloods$fli[i])
     monthlyFloods[nrow(monthlyFloods) + 1,] = c(someFloods$Year[i],10, someFloods$fli[i])
     monthlyFloods[nrow(monthlyFloods) + 1,] = c(someFloods$Year[i],11, someFloods$fli[i])     
  }
  if((someFloods$Season[i] == 'winter')  | (someFloods$Season[i] == 'undated'))   {
     monthlyFloods[nrow(monthlyFloods) + 1,] = c(someFloods$Year[i]-1,12, someFloods$fli[i])  
     monthlyFloods[nrow(monthlyFloods) + 1,] = c(someFloods$Year[i],1, someFloods$fli[i])
     monthlyFloods[nrow(monthlyFloods) + 1,] = c(someFloods$Year[i],2, someFloods$fli[i])     
  }
}
monthlyFloods <- subset(monthlyFloods, (monthlyFloods$year > 1499))

avgFloods = aggregate(monthlyFloods$fli, list(monthlyFloods$year,monthlyFloods$month), mean)  
names(avgFloods)[names(avgFloods) == 'Group.1'] <- 'year'
names(avgFloods)[names(avgFloods) == 'Group.2'] <- 'month'
names(avgFloods)[names(avgFloods) == 'x'] <- 'fli'
avgFloods$fli <- round(1.5*avgFloods$fli)

avgFloods$ts <- signif(avgFloods$year + (avgFloods$month-0.5)/12, digits=6)
avgFloods$time <- paste(avgFloods$year,avgFloods$month, '15 00:00:00', sep='-')
avgFloods <- avgFloods[order(avgFloods$ts),]

write.table(avgFloods, file = "csv/floods_de.csv", append = FALSE, quote = TRUE, sep = ",",
            eol = "\n", na = "NA", dec = ".", row.names = FALSE,
            col.names = TRUE, qmethod = "escape", fileEncoding = "UTF-8")
```

## Plot Floods


```r
require("ggplot2")
```

```
## Loading required package: ggplot2
```

```r
floods1500 <- read.csv("./csv/floods_de.csv", sep=",")
mp <- ggplot() +
      #geom_line(aes(y=volcano1500$vei, x=volcano1500$time), color="blue") +
      geom_segment(aes(y=floods1500$fli, yend=0, x=floods1500$ts, xend=floods1500$ts), color="blue") +
      xlab("Year") + ylab("FLI []")
mp
```

![](README_files/figure-html/plot-1.png)<!-- -->








