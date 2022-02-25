---
title: "Data Summary"
author: "Tyson Brost, Chase Hatch, Isaac Palmer, Rachel Robertson"
date: "February 24, 2022"
output:
  html_document:  
    keep_md: true
    toc: true
    toc_float: true
    code_folding: hide
    fig_height: 6
    fig_width: 12
    fig_align: 'center'
    theme: paper
---






```r
# Use this R-Chunk to import all your datasets!
Q1 <- read_csv("Q1.csv")
Q2 <- read_csv("Q2.csv")
Q3 <- read_csv("Q3.csv")
Q4_2020 <- read_csv("Q4(2020).csv")
```

## Background

I filtered the data down and performed visualization and summaries on Itinerary fares per person by quarter and flight type, I wanted to include international to give 3 flight types but there was no data on international flights. Some of the fares were skewed, tens of thousands for one person or 0, I assume these where private jets, or free company tours and dropped all data values that where below the 5th percentile or above the 95th percentile, I then further filtered to include simply only fares with a range from \$25 per person to \$1500 or less.

Data Source:
[Source](https://www.transtats.bts.gov/DL_SelectFields.asp?gnoyr_VQ=FKF&QO_fu146_anzr=b4vtv0%20n0q%20Qr56v0n6v10%20f748rB)

![Cheapair.com](Desired_Study.png)
This is a visual that shows the type of study we originally wanted to conduct. Their data is not open source and because finding data of this type was not possible we changed the data and remainder of the project to an anaylsis of quarterly trends by flight type.

## Data Wrangling


```r
# Use this R-Chunk to clean & wrangle your data!
FullDat <- rbind(Q1,Q2)
FullDat <- rbind(FullDat,Q3)
FullDat <- rbind(FullDat,Q4_2020)
FullDat <- FullDat[,-14]
FullDat$ITIN_GEO_TYPE <- factor(case_when(FullDat$ITIN_GEO_TYPE == 1 ~ "Non-Continguous Domestic", FullDat$ITIN_GEO_TYPE == 2 ~ "Continguous Domestic"))
FullDat_Filt <- filter(FullDat, ITIN_FARE >= 25 & ITIN_FARE <1500)




x.axis.breaks <- c(0,                      # binwidth = 100
                   seq(100, 500, 25),     # binwidth = 10
                   seq(600, 1500, 100))  # binwidth = 50

FullDat2_Non <- FullDat %>%
  filter(ITIN_GEO_TYPE == "Non-Continguous Domestic")

FullDat2_Non <- FullDat2_Non %>%
  mutate(Fare.cut = cut(ITIN_FARE,
                         breaks = x.axis.breaks)) %>%
  count(Fare.cut, ITIN_GEO_TYPE) %>%
  mutate(xmin = x.axis.breaks[as.integer(Fare.cut)],
         xmax = x.axis.breaks[as.integer(Fare.cut) + 1]) %>%
  group_by(Fare.cut) %>%
  arrange(desc(ITIN_GEO_TYPE)) %>%
  mutate(ymax = cumsum(n)) %>%
  mutate(ymin = lag(ymax)) %>%
  mutate(ymin = ifelse(is.na(ymin), 0, ymin)) %>%
  ungroup()

FullDat2_Con <- FullDat %>%
  filter(ITIN_GEO_TYPE == "Continguous Domestic")

FullDat2_Con <- FullDat2_Con %>%
  mutate(Fare.cut = cut(ITIN_FARE,
                         breaks = x.axis.breaks)) %>%
  count(Fare.cut, ITIN_GEO_TYPE) %>%
  mutate(xmin = x.axis.breaks[as.integer(Fare.cut)],
         xmax = x.axis.breaks[as.integer(Fare.cut) + 1]) %>%
  group_by(Fare.cut) %>%
  arrange(desc(ITIN_GEO_TYPE)) %>%
  mutate(ymax = cumsum(n)) %>%
  mutate(ymin = lag(ymax)) %>%
  mutate(ymin = ifelse(is.na(ymin), 0, ymin)) %>%
  ungroup()

FullDat2_Con$color <- "cyan3"
```

## Data Visualization


```r
# Use this R-Chunk to plot & visualize your data!
ggplot(data=FullDat_Filt, )+
  geom_violin(aes(x=ITIN_GEO_TYPE, y=ITIN_FARE, color=ITIN_GEO_TYPE))+
  #geom_sina(aes(x=as.factor(ITIN_GEO_TYPE), y=ITIN_FARE, color=as.factor(ITIN_GEO_TYPE)),alpha=0.15)+
  facet_grid(rows = ~QUARTER)+
  #scale_y_continuous(limits = quantile(FullDat$ITIN_FARE, c(0.05, 0.95)))+
  scale_y_continuous(breaks = waiver(), labels = waiver(), n.breaks = 7)+
  labs(color= "", title= "Distribution of fares by quarter")+ 
  ylab("Fare")+
  xlab("Flight Type")+
  #gghighlight() +
  coord_flip() +
  theme_bw()+
  theme(axis.text.y = element_text(angle = 45, vjust = 0.5, hjust=1))
```

![](DataSummary_files/figure-html/plot_data-1.png)<!-- -->



```r
ggplot(data=FullDat_Filt, ) +
    geom_histogram(aes(x=ITIN_FARE, fill= ITIN_GEO_TYPE)) +
    #geom_area(aes(x=HEPerGDP,y=child_mort, fill= continent))+
    theme_bw() +
    gghighlight() +
    facet_wrap(~ITIN_GEO_TYPE) +
    theme(
      panel.spacing = unit(0.5, "lines"),
      axis.ticks.x=element_blank()
    )+ 
  labs(fill = "Flight Type", title= "Distribution of Fares by Flight Type")+ 
  xlab("Fare per passenger") + ylab("Count")
```

![](DataSummary_files/figure-html/unnamed-chunk-2-1.png)<!-- -->


```r
ggplot(data=FullDat_Filt, ) +
    geom_histogram(aes(x=ITIN_FARE, fill= as.factor(QUARTER))) +
    #geom_area(aes(x=HEPerGDP,y=child_mort, fill= continent))+
    theme_bw() +
    gghighlight() +
    facet_grid(cols = vars(QUARTER)) +
    theme(
      panel.spacing = unit(0.5, "lines"),
      axis.ticks.x=element_blank()
    )+ 
  labs(fill = "Flight Type", title= "Distribution of Fares by Quarter")+ 
  xlab("Fare per passenger") + ylab("Count")
```

![](DataSummary_files/figure-html/unnamed-chunk-3-1.png)<!-- -->


```r
p_non <- ggplot(FullDat2_Non %>% drop_na(),
       aes(xmin = xmin, xmax = xmax, ymin = ymin/1000, ymax = ymax/1000)) +
  geom_rect() +
  facet_grid(rows= ~ITIN_GEO_TYPE)+
  theme_bw()

p_non + 
  facet_zoom(x = xmin >= 100 & xmax <= 500) +
  scale_x_continuous(expand = c(0, 0))+
  labs(title= "Distribution of Fares for Non-Contiguous flights")+ 
  xlab("Fare per passenger") + ylab("Count (Thousands)")
```

![](DataSummary_files/figure-html/unnamed-chunk-4-1.png)<!-- -->


```r
p_con <- ggplot(FullDat2_Con %>% drop_na(),
       aes(xmin = xmin, xmax = xmax, ymin = ymin/1000, ymax = ymax/1000)) +
  geom_rect() +
  facet_grid(rows= ~ITIN_GEO_TYPE)+
  scale_y_continuous( c(0, 1000000))+
  theme_bw()

p_con + 
  facet_zoom(x = xmin >= 100 & xmax <= 500) +
  scale_x_continuous(expand = c(0, 0))+
  labs(title= "Distribution of Fares for Contiguous flights")+ 
  xlab("Fare per passenger") + ylab("Count (Thousands)")
```

![](DataSummary_files/figure-html/unnamed-chunk-5-1.png)<!-- -->



```r
pander(favstats(ITIN_FARE ~ ITIN_GEO_TYPE+QUARTER, data=FullDat_Filt)[c("ITIN_GEO_TYPE.QUARTER", "Q1","median", "mean","Q3", "sd","n")], caption= "Summary table of Fares by Flight Type per Quarter")
```


---------------------------------------------------------------------------
   ITIN_GEO_TYPE.QUARTER      Q1    median   mean    Q3     sd        n    
---------------------------- ----- -------- ------- ----- ------- ---------
   Continguous Domestic.1     161    262     302.9   387    202    1686461 

 Non-Continguous Domestic.1   215    345     408.7   535   269.9   136700  

   Continguous Domestic.2     193    303     347.6   446   217.6    3e+06  

 Non-Continguous Domestic.2   259    408     468.8   617   282.1   258253  

   Continguous Domestic.3     204    317      361    463   221.8   3460278 

 Non-Continguous Domestic.3   274    432     489.6   642   288.2   282181  

   Continguous Domestic.4     162    260     300.1   387   196.3   1690822 

 Non-Continguous Domestic.4   226    365     427.9   568   278.6   101063  
---------------------------------------------------------------------------

Table: Summary table of Fares by Flight Type per Quarter


## Conclusions
