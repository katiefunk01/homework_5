---
title: 'Weekly Exercises #5'
author: "Katie Funk"
output: 
  html_document:
    keep_md: TRUE
    toc: TRUE
    toc_float: TRUE
    df_print: paged
    code_download: true
    theme: journal
    highlight: kate
---





```r
library(tidyverse)     # for data cleaning and plotting
library(gardenR)       # for Lisa's garden data
library(lubridate)     # for date manipulation
library(openintro)     # for the abbr2state() function
library(palmerpenguins)# for Palmer penguin data
library(maps)          # for map data
library(ggmap)         # for mapping points on maps
library(gplots)        # for col2hex() function
library(RColorBrewer)  # for color palettes
library(sf)            # for working with spatial data
library(leaflet)       # for highly customizable mapping
library(ggthemes)      # for more themes (including theme_map())
library(plotly)        # for the ggplotly() - basic interactivity
library(gganimate)     # for adding animation layers to ggplots
library(transformr)    # for "tweening" (gganimate)
library(gifski)        # need the library for creating gifs but don't need to load each time
library(shiny)         # for creating interactive apps
library(babynames)
library(ggtext)
theme_set(theme_minimal())
```


```r
# SNCF Train data
small_trains <- read_csv("https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2019/2019-02-26/small_trains.csv") 

# Lisa's garden data
data("garden_harvest")

# Lisa's Mallorca cycling data
mallorca_bike_day7 <- read_csv("https://www.dropbox.com/s/zc6jan4ltmjtvy0/mallorca_bike_day7.csv?dl=1") %>% 
  select(1:4, speed)

# Heather Lendway's Ironman 70.3 Pan Am championships Panama data
panama_swim <- read_csv("https://raw.githubusercontent.com/llendway/gps-data/master/data/panama_swim_20160131.csv")

panama_bike <- read_csv("https://raw.githubusercontent.com/llendway/gps-data/master/data/panama_bike_20160131.csv")

panama_run <- read_csv("https://raw.githubusercontent.com/llendway/gps-data/master/data/panama_run_20160131.csv")

#COVID-19 data from the New York Times
covid19 <- read_csv("https://raw.githubusercontent.com/nytimes/covid-19-data/master/us-states.csv")
```

## Put your homework on GitHub!

Go [here](https://github.com/llendway/github_for_collaboration/blob/master/github_for_collaboration.md) or to previous homework to remind yourself how to get set up. 

Once your repository is created, you should always open your **project** rather than just opening an .Rmd file. You can do that by either clicking on the .Rproj file in your repository folder on your computer. Or, by going to the upper right hand corner in R Studio and clicking the arrow next to where it says Project: (None). You should see your project come up in that list if you've used it recently. You could also go to File --> Open Project and navigate to your .Rproj file. 

## Instructions

* Put your name at the top of the document. 

* **For ALL graphs, you should include appropriate labels.** 

* Feel free to change the default theme, which I currently have set to `theme_minimal()`. 

* Use good coding practice. Read the short sections on good code with [pipes](https://style.tidyverse.org/pipes.html) and [ggplot2](https://style.tidyverse.org/ggplot2.html). **This is part of your grade!**

* **NEW!!** With animated graphs, add `eval=FALSE` to the code chunk that creates the animation and saves it using `anim_save()`. Add another code chunk to reread the gif back into the file. See the [tutorial](https://animation-and-interactivity-in-r.netlify.app/) for help. 

* When you are finished with ALL the exercises, uncomment the options at the top so your document looks nicer. Don't do it before then, or else you might miss some important warnings and messages.

## Warm-up exercises from tutorial

  1. Choose 2 graphs you have created for ANY assignment in this class and add interactivity using the `ggplotly()` function.
  

```r
# Graph 1
top_variety_weight <- garden_harvest %>%
  filter(vegetable %in% c("tomatoes","pumpkins", "zucchini", "squash", "cucumbers")) %>%
  group_by(variety, vegetable) %>%
  mutate(weight_lbs = weight*0.00220462) %>%
  summarise(cum_weight_lbs = sum(weight_lbs)) %>%
  mutate(vegetable_capital = str_to_title(vegetable),
         variety_capital = str_to_title(variety)) %>%
  group_by(vegetable_capital) %>%
  slice_max(cum_weight_lbs, n = 1)


garden_1 <-garden_harvest %>%
  filter(vegetable %in% c("tomatoes","pumpkins", "zucchini", "squash", "cucumbers")) %>%
  group_by(vegetable) %>%
  mutate(weight_lbs = weight*0.00220462) %>%
  summarise(cum_weight_lbs = sum(weight_lbs)) %>%
  ungroup() %>%
  mutate(vegetable_capital = str_to_title(vegetable)) %>%
  ggplot(aes(y = fct_reorder(vegetable_capital, cum_weight_lbs),
             x = cum_weight_lbs)) +
  geom_col(fill = "lightcoral") +
  geom_col(data = top_variety_weight, aes(y = vegetable_capital,
                                          text = variety_capital),
           fill = "rosybrown2") +
  geom_text(data = top_variety_weight, aes(y = vegetable_capital, label = variety_capital), hjust = 0) +
  theme(plot.title = element_markdown(),
       panel.grid.major = element_blank() ) +
  labs(title = "<span style = 'color: olivedrab;'>Vegetables With the Highest Cumulative Weights: Summer 2020(lbs) </span>",
       x = "",
       y = "",
       caption = "Visualization: Katie Funk | Data: Lisa Lendway's Garden",
       color = "Vegetable Variety") +
  scale_x_continuous(expand = c(0,0),
                    breaks = c( 50, 100, 150, 200, 250, 300, 350)) 

ggplotly(garden_1,
         tooltip = c("text","x"))
```

```{=html}
<div id="htmlwidget-da0b720d4cbcc67c4a63" style="width:672px;height:480px;" class="plotly html-widget"></div>
<script type="application/json" data-for="htmlwidget-da0b720d4cbcc67c4a63">{"x":{"data":[{"orientation":"v","width":[43.60958822,154.6651161,99.02491654,348.83922722,99.70834874],"base":[0.55,3.55,1.55,4.55,2.55],"x":[21.80479411,77.33255805,49.51245827,174.41961361,49.85417437],"y":[0.9,0.9,0.9,0.9,0.9],"text":["cum_weight_lbs:  43.60959","cum_weight_lbs: 154.66512","cum_weight_lbs:  99.02492","cum_weight_lbs: 348.83923","cum_weight_lbs:  99.70835"],"type":"bar","marker":{"autocolorscale":false,"color":"rgba(240,128,128,1)","line":{"width":1.88976377952756,"color":"transparent"}},"showlegend":false,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"orientation":"v","width":[43.60958822,76.93241952,41.5240177,65.67342518,99.70834874],"base":[0.55,3.55,1.55,4.55,2.55],"x":[21.80479411,38.46620976,20.76200885,32.83671259,49.85417437],"y":[0.9,0.9,0.9,0.9,0.9],"text":["cum_weight_lbs: 43.60959<br />Pickling","cum_weight_lbs: 76.93242<br />Saved","cum_weight_lbs: 41.52402<br />Blue (Saved)","cum_weight_lbs: 65.67343<br />Amish Paste","cum_weight_lbs: 99.70835<br />Romanesco"],"type":"bar","marker":{"autocolorscale":false,"color":"rgba(238,180,180,1)","line":{"width":1.88976377952756,"color":"transparent"}},"showlegend":false,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[43.60958822,76.93241952,41.5240177,65.67342518,99.70834874],"y":[1,4,2,5,3],"text":["Pickling","Saved","Blue (Saved)","Amish Paste","Romanesco"],"hovertext":["cum_weight_lbs: 43.60959","cum_weight_lbs: 76.93242","cum_weight_lbs: 41.52402","cum_weight_lbs: 65.67343","cum_weight_lbs: 99.70835"],"textfont":{"size":14.6645669291339,"color":"rgba(0,0,0,1)"},"type":"scatter","mode":"text","hoveron":"points","showlegend":false,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null}],"layout":{"margin":{"t":43.7625570776256,"r":7.30593607305936,"b":25.5707762557078,"l":63.5616438356165},"font":{"color":"rgba(0,0,0,1)","family":"","size":14.6118721461187},"title":{"text":"<span style = 'color: olivedrab;'>Vegetables With the Highest Cumulative Weights: Summer 2020(lbs) <\/span>","font":{"color":"rgba(0,0,0,1)","family":"","size":17.5342465753425},"x":0,"xref":"paper"},"xaxis":{"domain":[0,1],"automargin":true,"type":"linear","autorange":false,"range":[0,348.83922722],"tickmode":"array","ticktext":["50","100","150","200","250","300"],"tickvals":[50,100,150,200,250,300],"categoryorder":"array","categoryarray":["50","100","150","200","250","300"],"nticks":null,"ticks":"","tickcolor":null,"ticklen":3.65296803652968,"tickwidth":0,"showticklabels":true,"tickfont":{"color":"rgba(77,77,77,1)","family":"","size":11.689497716895},"tickangle":-0,"showline":false,"linecolor":null,"linewidth":0,"showgrid":false,"gridcolor":null,"gridwidth":0,"zeroline":false,"anchor":"y","title":{"text":"","font":{"color":"rgba(0,0,0,1)","family":"","size":14.6118721461187}},"hoverformat":".2f"},"yaxis":{"domain":[0,1],"automargin":true,"type":"linear","autorange":false,"range":[0.4,5.6],"tickmode":"array","ticktext":["Cucumbers","Squash","Zucchini","Pumpkins","Tomatoes"],"tickvals":[1,2,3,4,5],"categoryorder":"array","categoryarray":["Cucumbers","Squash","Zucchini","Pumpkins","Tomatoes"],"nticks":null,"ticks":"","tickcolor":null,"ticklen":3.65296803652968,"tickwidth":0,"showticklabels":true,"tickfont":{"color":"rgba(77,77,77,1)","family":"","size":11.689497716895},"tickangle":-0,"showline":false,"linecolor":null,"linewidth":0,"showgrid":false,"gridcolor":null,"gridwidth":0,"zeroline":false,"anchor":"x","title":{"text":"","font":{"color":"rgba(0,0,0,1)","family":"","size":14.6118721461187}},"hoverformat":".2f"},"shapes":[{"type":"rect","fillcolor":null,"line":{"color":null,"width":0,"linetype":[]},"yref":"paper","xref":"paper","x0":0,"x1":1,"y0":0,"y1":1}],"showlegend":false,"legend":{"bgcolor":null,"bordercolor":null,"borderwidth":0,"font":{"color":"rgba(0,0,0,1)","family":"","size":11.689497716895}},"hovermode":"closest","barmode":"relative"},"config":{"doubleClick":"reset","showSendToCloud":false},"source":"A","attrs":{"83561dcedd3c":{"x":{},"y":{},"type":"bar"},"8356781e9d6d":{"x":{},"y":{},"text":{}},"835621f19cb7":{"x":{},"y":{},"label":{}}},"cur_data":"83561dcedd3c","visdat":{"83561dcedd3c":["function (y) ","x"],"8356781e9d6d":["function (y) ","x"],"835621f19cb7":["function (y) ","x"]},"highlight":{"on":"plotly_click","persistent":false,"dynamic":false,"selectize":false,"opacityDim":0.2,"selected":{"opacity":1},"debounce":0},"shinyEvents":["plotly_hover","plotly_click","plotly_selected","plotly_relayout","plotly_brushed","plotly_brushing","plotly_clickannotation","plotly_doubleclick","plotly_deselect","plotly_afterplot","plotly_sunburstclick"],"base_url":"https://plot.ly"},"evals":[],"jsHooks":[]}</script>
```




```r
# Graph 2
those_names <-babynames %>%
  group_by(year, sex) %>%
  summarize(unique_names = sum(n)) %>%
  ggplot(aes(y = unique_names, x = year, color = sex,
             text = unique_names)) + 
  geom_point() +
  theme_clean() +
  scale_color_canva() +
  labs(title = "Unique baby names  over time",
       x = "Year",
       y = "Number of names")

ggplotly(those_names,
         tooltip = c("text", "x"))
```

```{=html}
<div id="htmlwidget-3e35e223b1d3abe47b91" style="width:672px;height:480px;" class="plotly html-widget"></div>
<script type="application/json" data-for="htmlwidget-3e35e223b1d3abe47b91">{"x":{"data":[{"x":[1880,1881,1882,1883,1884,1885,1886,1887,1888,1889,1890,1891,1892,1893,1894,1895,1896,1897,1898,1899,1900,1901,1902,1903,1904,1905,1906,1907,1908,1909,1910,1911,1912,1913,1914,1915,1916,1917,1918,1919,1920,1921,1922,1923,1924,1925,1926,1927,1928,1929,1930,1931,1932,1933,1934,1935,1936,1937,1938,1939,1940,1941,1942,1943,1944,1945,1946,1947,1948,1949,1950,1951,1952,1953,1954,1955,1956,1957,1958,1959,1960,1961,1962,1963,1964,1965,1966,1967,1968,1969,1970,1971,1972,1973,1974,1975,1976,1977,1978,1979,1980,1981,1982,1983,1984,1985,1986,1987,1988,1989,1990,1991,1992,1993,1994,1995,1996,1997,1998,1999,2000,2001,2002,2003,2004,2005,2006,2007,2008,2009,2010,2011,2012,2013,2014,2015,2016,2017],"y":[90993,91953,107847,112319,129020,133055,144533,145981,178622,178366,190376,185481,212339,212905,222921,233627,237918,234200,258768,233023,299800,239345,264076,261971,275364,291619,295303,318566,334313,347230,396502,418309,558117,624545,761576,983867,1044340,1081282,1157646,1130130,1198290,1233048,1200975,1206369,1248891,1217382,1185379,1192399,1153221,1116517,1125885,1064620,1067071,1007826,1044109,1048493,1040309,1063918,1103340,1096596,1143393,1207984,1350600,1395165,1327153,1307020,1571225,1772694,1697793,1710832,1713151,1800880,1854608,1880924,1941857,1955034,2007432,2044249,2010952,2023092,2022018,2017591,1966565,1927286,1894740,1765073,1691945,1650865,1640215,1686999,1748175,1663573,1521150,1458168,1467416,1457772,1465106,1533098,1531789,1605200,1660147,1667773,1693053,1670371,1683380,1720425,1715074,1738220,1780268,1843258,1898046,1874620,1843509,1808635,1785304,1758166,1752560,1740203,1766358,1773146,1815110,1799636,1795741,1825950,1834856,1846258,1899526,1920619,1888607,1834599,1774758,1756004,1757328,1750321,1781072,1778883,1763916,1711811],"text":["year: 1880<br />  90993","year: 1881<br />  91953","year: 1882<br /> 107847","year: 1883<br /> 112319","year: 1884<br /> 129020","year: 1885<br /> 133055","year: 1886<br /> 144533","year: 1887<br /> 145981","year: 1888<br /> 178622","year: 1889<br /> 178366","year: 1890<br /> 190376","year: 1891<br /> 185481","year: 1892<br /> 212339","year: 1893<br /> 212905","year: 1894<br /> 222921","year: 1895<br /> 233627","year: 1896<br /> 237918","year: 1897<br /> 234200","year: 1898<br /> 258768","year: 1899<br /> 233023","year: 1900<br /> 299800","year: 1901<br /> 239345","year: 1902<br /> 264076","year: 1903<br /> 261971","year: 1904<br /> 275364","year: 1905<br /> 291619","year: 1906<br /> 295303","year: 1907<br /> 318566","year: 1908<br /> 334313","year: 1909<br /> 347230","year: 1910<br /> 396502","year: 1911<br /> 418309","year: 1912<br /> 558117","year: 1913<br /> 624545","year: 1914<br /> 761576","year: 1915<br /> 983867","year: 1916<br />1044340","year: 1917<br />1081282","year: 1918<br />1157646","year: 1919<br />1130130","year: 1920<br />1198290","year: 1921<br />1233048","year: 1922<br />1200975","year: 1923<br />1206369","year: 1924<br />1248891","year: 1925<br />1217382","year: 1926<br />1185379","year: 1927<br />1192399","year: 1928<br />1153221","year: 1929<br />1116517","year: 1930<br />1125885","year: 1931<br />1064620","year: 1932<br />1067071","year: 1933<br />1007826","year: 1934<br />1044109","year: 1935<br />1048493","year: 1936<br />1040309","year: 1937<br />1063918","year: 1938<br />1103340","year: 1939<br />1096596","year: 1940<br />1143393","year: 1941<br />1207984","year: 1942<br />1350600","year: 1943<br />1395165","year: 1944<br />1327153","year: 1945<br />1307020","year: 1946<br />1571225","year: 1947<br />1772694","year: 1948<br />1697793","year: 1949<br />1710832","year: 1950<br />1713151","year: 1951<br />1800880","year: 1952<br />1854608","year: 1953<br />1880924","year: 1954<br />1941857","year: 1955<br />1955034","year: 1956<br />2007432","year: 1957<br />2044249","year: 1958<br />2010952","year: 1959<br />2023092","year: 1960<br />2022018","year: 1961<br />2017591","year: 1962<br />1966565","year: 1963<br />1927286","year: 1964<br />1894740","year: 1965<br />1765073","year: 1966<br />1691945","year: 1967<br />1650865","year: 1968<br />1640215","year: 1969<br />1686999","year: 1970<br />1748175","year: 1971<br />1663573","year: 1972<br />1521150","year: 1973<br />1458168","year: 1974<br />1467416","year: 1975<br />1457772","year: 1976<br />1465106","year: 1977<br />1533098","year: 1978<br />1531789","year: 1979<br />1605200","year: 1980<br />1660147","year: 1981<br />1667773","year: 1982<br />1693053","year: 1983<br />1670371","year: 1984<br />1683380","year: 1985<br />1720425","year: 1986<br />1715074","year: 1987<br />1738220","year: 1988<br />1780268","year: 1989<br />1843258","year: 1990<br />1898046","year: 1991<br />1874620","year: 1992<br />1843509","year: 1993<br />1808635","year: 1994<br />1785304","year: 1995<br />1758166","year: 1996<br />1752560","year: 1997<br />1740203","year: 1998<br />1766358","year: 1999<br />1773146","year: 2000<br />1815110","year: 2001<br />1799636","year: 2002<br />1795741","year: 2003<br />1825950","year: 2004<br />1834856","year: 2005<br />1846258","year: 2006<br />1899526","year: 2007<br />1920619","year: 2008<br />1888607","year: 2009<br />1834599","year: 2010<br />1774758","year: 2011<br />1756004","year: 2012<br />1757328","year: 2013<br />1750321","year: 2014<br />1781072","year: 2015<br />1778883","year: 2016<br />1763916","year: 2017<br />1711811"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(249,136,102,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(249,136,102,1)"}},"hoveron":"points","name":"F","legendgroup":"F","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[1880,1881,1882,1883,1884,1885,1886,1887,1888,1889,1890,1891,1892,1893,1894,1895,1896,1897,1898,1899,1900,1901,1902,1903,1904,1905,1906,1907,1908,1909,1910,1911,1912,1913,1914,1915,1916,1917,1918,1919,1920,1921,1922,1923,1924,1925,1926,1927,1928,1929,1930,1931,1932,1933,1934,1935,1936,1937,1938,1939,1940,1941,1942,1943,1944,1945,1946,1947,1948,1949,1950,1951,1952,1953,1954,1955,1956,1957,1958,1959,1960,1961,1962,1963,1964,1965,1966,1967,1968,1969,1970,1971,1972,1973,1974,1975,1976,1977,1978,1979,1980,1981,1982,1983,1984,1985,1986,1987,1988,1989,1990,1991,1992,1993,1994,1995,1996,1997,1998,1999,2000,2001,2002,2003,2004,2005,2006,2007,2008,2009,2010,2011,2012,2013,2014,2015,2016,2017],"y":[110491,100743,113686,104627,114442,107799,110784,101413,120851,110580,111025,101191,122036,112318,115769,117395,119567,112757,122690,106210,150483,106469,122660,119233,128125,132312,133158,146829,154339,163998,194213,225970,429947,512566,654767,848594,890094,925502,1013504,980136,1064391,1101463,1088221,1096158,1132783,1115944,1110558,1126875,1107618,1075534,1097274,1039451,1044010,991315,1032398,1041103,1037063,1066452,1108962,1106680,1158985,1228076,1380857,1426962,1362664,1345618,1623821,1829338,1754424,1773699,1790549,1882982,1944564,1971304,2038027,2059007,2113638,2155758,2120705,2133342,2132359,2122653,2068669,2031505,1993060,1860956,1783729,1744265,1738661,1789216,1859245,1769012,1622477,1559244,1572993,1562138,1569843,1643766,1642479,1722216,1784390,1791409,1814611,1792455,1804440,1847650,1840797,1866183,1913203,2000301,2052946,2019709,1996687,1960647,1931454,1903185,1893802,1884596,1910749,1919391,1962969,1941815,1940301,1974021,1983505,1995746,2053406,2073388,2037751,1981039,1915942,1895910,1893134,1886989,1915239,1909804,1889052,1834490],"text":["year: 1880<br /> 110491","year: 1881<br /> 100743","year: 1882<br /> 113686","year: 1883<br /> 104627","year: 1884<br /> 114442","year: 1885<br /> 107799","year: 1886<br /> 110784","year: 1887<br /> 101413","year: 1888<br /> 120851","year: 1889<br /> 110580","year: 1890<br /> 111025","year: 1891<br /> 101191","year: 1892<br /> 122036","year: 1893<br /> 112318","year: 1894<br /> 115769","year: 1895<br /> 117395","year: 1896<br /> 119567","year: 1897<br /> 112757","year: 1898<br /> 122690","year: 1899<br /> 106210","year: 1900<br /> 150483","year: 1901<br /> 106469","year: 1902<br /> 122660","year: 1903<br /> 119233","year: 1904<br /> 128125","year: 1905<br /> 132312","year: 1906<br /> 133158","year: 1907<br /> 146829","year: 1908<br /> 154339","year: 1909<br /> 163998","year: 1910<br /> 194213","year: 1911<br /> 225970","year: 1912<br /> 429947","year: 1913<br /> 512566","year: 1914<br /> 654767","year: 1915<br /> 848594","year: 1916<br /> 890094","year: 1917<br /> 925502","year: 1918<br />1013504","year: 1919<br /> 980136","year: 1920<br />1064391","year: 1921<br />1101463","year: 1922<br />1088221","year: 1923<br />1096158","year: 1924<br />1132783","year: 1925<br />1115944","year: 1926<br />1110558","year: 1927<br />1126875","year: 1928<br />1107618","year: 1929<br />1075534","year: 1930<br />1097274","year: 1931<br />1039451","year: 1932<br />1044010","year: 1933<br /> 991315","year: 1934<br />1032398","year: 1935<br />1041103","year: 1936<br />1037063","year: 1937<br />1066452","year: 1938<br />1108962","year: 1939<br />1106680","year: 1940<br />1158985","year: 1941<br />1228076","year: 1942<br />1380857","year: 1943<br />1426962","year: 1944<br />1362664","year: 1945<br />1345618","year: 1946<br />1623821","year: 1947<br />1829338","year: 1948<br />1754424","year: 1949<br />1773699","year: 1950<br />1790549","year: 1951<br />1882982","year: 1952<br />1944564","year: 1953<br />1971304","year: 1954<br />2038027","year: 1955<br />2059007","year: 1956<br />2113638","year: 1957<br />2155758","year: 1958<br />2120705","year: 1959<br />2133342","year: 1960<br />2132359","year: 1961<br />2122653","year: 1962<br />2068669","year: 1963<br />2031505","year: 1964<br />1993060","year: 1965<br />1860956","year: 1966<br />1783729","year: 1967<br />1744265","year: 1968<br />1738661","year: 1969<br />1789216","year: 1970<br />1859245","year: 1971<br />1769012","year: 1972<br />1622477","year: 1973<br />1559244","year: 1974<br />1572993","year: 1975<br />1562138","year: 1976<br />1569843","year: 1977<br />1643766","year: 1978<br />1642479","year: 1979<br />1722216","year: 1980<br />1784390","year: 1981<br />1791409","year: 1982<br />1814611","year: 1983<br />1792455","year: 1984<br />1804440","year: 1985<br />1847650","year: 1986<br />1840797","year: 1987<br />1866183","year: 1988<br />1913203","year: 1989<br />2000301","year: 1990<br />2052946","year: 1991<br />2019709","year: 1992<br />1996687","year: 1993<br />1960647","year: 1994<br />1931454","year: 1995<br />1903185","year: 1996<br />1893802","year: 1997<br />1884596","year: 1998<br />1910749","year: 1999<br />1919391","year: 2000<br />1962969","year: 2001<br />1941815","year: 2002<br />1940301","year: 2003<br />1974021","year: 2004<br />1983505","year: 2005<br />1995746","year: 2006<br />2053406","year: 2007<br />2073388","year: 2008<br />2037751","year: 2009<br />1981039","year: 2010<br />1915942","year: 2011<br />1895910","year: 2012<br />1893134","year: 2013<br />1886989","year: 2014<br />1915239","year: 2015<br />1909804","year: 2016<br />1889052","year: 2017<br />1834490"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(255,66,14,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(255,66,14,1)"}},"hoveron":"points","name":"M","legendgroup":"M","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null}],"layout":{"margin":{"t":45.7550850975509,"r":7.97011207970112,"b":40.381901203819,"l":67.0817766708178},"paper_bgcolor":"rgba(255,255,255,1)","font":{"color":"rgba(0,0,0,1)","family":"sans","size":15.9402241594022},"title":{"text":"<b> Unique baby names  over time <\/b>","font":{"color":"rgba(0,0,0,1)","family":"sans","size":18.5969281859693},"x":0,"xref":"paper"},"xaxis":{"domain":[0,1],"automargin":true,"type":"linear","autorange":false,"range":[1873.15,2023.85],"tickmode":"array","ticktext":["1880","1920","1960","2000"],"tickvals":[1880,1920,1960,2000],"categoryorder":"array","categoryarray":["1880","1920","1960","2000"],"nticks":null,"ticks":"outside","tickcolor":"rgba(0,0,0,1)","ticklen":3.98505603985056,"tickwidth":0.724555643609193,"showticklabels":true,"tickfont":{"color":"rgba(0,0,0,1)","family":"sans","size":11.9551681195517},"tickangle":-0,"showline":true,"linecolor":"rgba(0,0,0,1)","linewidth":0.66417600664176,"showgrid":false,"gridcolor":null,"gridwidth":0,"zeroline":false,"anchor":"y","title":{"text":"Year","font":{"color":"rgba(0,0,0,1)","family":"sans","size":13.2835201328352}},"hoverformat":".2f"},"yaxis":{"domain":[0,1],"automargin":true,"type":"linear","autorange":false,"range":[-12245.25,2258996.25],"tickmode":"array","ticktext":["0","500000","1000000","1500000","2000000"],"tickvals":[0,500000,1000000,1500000,2000000],"categoryorder":"array","categoryarray":["0","500000","1000000","1500000","2000000"],"nticks":null,"ticks":"outside","tickcolor":"rgba(0,0,0,1)","ticklen":3.98505603985056,"tickwidth":0.724555643609193,"showticklabels":true,"tickfont":{"color":"rgba(0,0,0,1)","family":"sans","size":11.9551681195517},"tickangle":-0,"showline":true,"linecolor":"rgba(0,0,0,1)","linewidth":0.66417600664176,"showgrid":true,"gridcolor":"rgba(190,190,190,1)","gridwidth":0.724555643609193,"zeroline":false,"anchor":"x","title":{"text":"Number of names","font":{"color":"rgba(0,0,0,1)","family":"sans","size":13.2835201328352}},"hoverformat":".2f"},"shapes":[{"type":"rect","fillcolor":null,"line":{"color":null,"width":0,"linetype":[]},"yref":"paper","xref":"paper","x0":0,"x1":1,"y0":0,"y1":1}],"showlegend":true,"legend":{"bgcolor":"rgba(255,255,255,1)","bordercolor":"rgba(0,0,0,1)","borderwidth":2.06156048675734,"font":{"color":"rgba(0,0,0,1)","family":"sans","size":14.6118721461187},"y":0.905511811023622},"annotations":[{"text":"sex","x":1.02,"y":1,"showarrow":false,"ax":0,"ay":0,"font":{"color":"rgba(0,0,0,1)","family":"sans","size":15.9402241594022},"xref":"paper","yref":"paper","textangle":-0,"xanchor":"left","yanchor":"bottom","legendTitle":true}],"hovermode":"closest","barmode":"relative"},"config":{"doubleClick":"reset","showSendToCloud":false},"source":"A","attrs":{"835637f0c7ea":{"x":{},"y":{},"colour":{},"text":{},"type":"scatter"}},"cur_data":"835637f0c7ea","visdat":{"835637f0c7ea":["function (y) ","x"]},"highlight":{"on":"plotly_click","persistent":false,"dynamic":false,"selectize":false,"opacityDim":0.2,"selected":{"opacity":1},"debounce":0},"shinyEvents":["plotly_hover","plotly_click","plotly_selected","plotly_relayout","plotly_brushed","plotly_brushing","plotly_clickannotation","plotly_doubleclick","plotly_deselect","plotly_afterplot","plotly_sunburstclick"],"base_url":"https://plot.ly"},"evals":[],"jsHooks":[]}</script>
```



  
  2. Use animation to tell an interesting story with the `small_trains` dataset that contains data from the SNCF (National Society of French Railways). These are Tidy Tuesday data! Read more about it [here](https://github.com/rfordatascience/tidytuesday/tree/master/data/2019/2019-02-26).

```r
small_trains %>%
  group_by(year, departure_station) %>%
  summarise(late_arrivals = sum(num_arriving_late)) %>%
  ggplot(aes(y = fct_reorder(departure_station, late_arrivals), x = late_arrivals,
             group = year)) +
  geom_col(fill = "palegreen4") +
  scale_x_continuous(expand = c(0,0)) +
  labs(title = "Late Arrivals by Station",
       subtitle = "Year: {closest_state}",
       y = "",
       x = "") +
  theme(plot.title = element_text(hjust = -1.25)) +
  transition_states(year,
                    transition_length = 1.5, 
                    state_length = 1) 

anim_save("late_trains_year.gif")
```


```r
knitr::include_graphics("late_trains_year.gif")
```

![](late_trains_year.gif)<!-- -->


## Garden data

  3. In this exercise, you will create a stacked area plot that reveals itself over time (see the `geom_area()` examples [here](https://ggplot2.tidyverse.org/reference/position_stack.html)). You will look at cumulative harvest of tomato varieties over time. You should do the following:
  * From the `garden_harvest` data, filter the data to the tomatoes and find the *daily* harvest in pounds for each variety.  
  * Then, for each variety, find the cumulative harvest in pounds.  
  * Use the data you just made to create a static cumulative harvest area plot, with the areas filled with different colors for each vegetable and arranged (HINT: `fct_reorder()`) from most to least harvested (most on the bottom).  
  * Add animation to reveal the plot over date. 

I have started the code for you below. The `complete()` function creates a row for all unique `date`/`variety` combinations. If a variety is not harvested on one of the harvest dates in the dataset, it is filled with a value of 0.


```r
cum_tomato_harvest<- garden_harvest %>% 
  filter(vegetable == "tomatoes") %>% 
  group_by(date, variety) %>% 
  summarize(daily_harvest_lb = sum(weight)*0.00220462) %>% 
  ungroup() %>% 
  complete(variety, date, fill = list(daily_harvest_lb = 0)) %>%
  group_by(variety) %>%
  mutate(cum_harvest_lb = cumsum(daily_harvest_lb)*0.00220462) %>% 
  ungroup() %>%
  ggplot(aes(x = date, y = cum_harvest_lb, fill = fct_reorder(variety, cum_harvest_lb,.desc = TRUE))) +
  geom_area() +
  scale_fill_manual(values = c("volunteers" = "darkseagreen4",
                                "Brandywine" = "tomato2",
                                "Jet Star" = "tomato4",
                                "Big Beef" = "brown3",
                                "Old German" = "firebrick4",
                                "Mortgage Lifter" = "darkgreen",
                                "Black Krim" = "olivedrab4",
                                "Cherokee Purple" = "firebrick",
                                "grape" = "indianred4",
                                "Amish Paste" = "springgreen4",
                                "Bonny Best" = "orangered4",
                                "Better Boy" = "chartreuse4")) +
  labs(title = "Cumulative Weight of Tomatoes Over Time: Summer 2020 (lbs)",
       y = "",
       x = "",
       fill = "Variety") +
  transition_reveal(date) 

animate(cum_tomato_harvest, nframes = 100, duration = 8,
        renderer = gifski_renderer())

anim_save("cum_tomato_weight.gif")
```


```r
knitr::include_graphics("cum_tomato_weight.gif")
```

![](cum_tomato_weight.gif)<!-- -->

## Maps, animation, and movement!

  4. Map my `mallorca_bike_day7` bike ride using animation! 
  Requirements:
  * Plot on a map using `ggmap`.  
  * Show "current" location with a red point. 
  * Show path up until the current point.  
  * Color the path according to elevation.  
  * Show the time in the subtitle.  
  * CHALLENGE: use the `ggimage` package and `geom_image` to add a bike image instead of a red point. You can use [this](https://raw.githubusercontent.com/llendway/animation_and_interactivity/master/bike.png) image. See [here](https://goodekat.github.io/presentations/2019-isugg-gganimate-spooky/slides.html#35) for an example. 
  * Add something of your own! And comment on if you prefer this to the static map and why or why not.
 

```r
mallorca_map <- get_stamenmap(
    bbox = c(left = 2.28 , bottom = 39.41, right = 3.03, top = 39.8), 
    maptype = "terrain",
    zoom = 11)

mallorca_bike_ride <- ggmap(mallorca_map) +
  geom_path(data = mallorca_bike_day7 , 
             aes(x =  lon, y = lat, color = ele),
             size = 1) +
  annotate("point", x = 2.586352, y = 39.66012, color = "red") +
  transition_reveal(time) +
  labs(title = "Biking in Mallorca",
       subtitle = "Time: {frame_along}",
       color = "Elevation") +
  scale_color_viridis_c(option = "magma") +
  theme_map() +
  theme(legend.background = element_blank())
  
animate(mallorca_bike_ride, nframes = 100,
        duration = 8, renderer = gifski_renderer())

anim_save("mallorca_bike_ride.gif")
```
 

```r
knitr::include_graphics("mallorca_bike_ride.gif")
```

![](mallorca_bike_ride.gif)<!-- -->

> I like this map better than the static one because it is more interactive and gives you more to look at.

  5. In this exercise, you get to meet my sister, Heather! She is a proud Mac grad, currently works as a Data Scientist at 3M where she uses R everyday, and for a few years (while still holding a full-time job) she was a pro triathlete. You are going to map one of her races. The data from each discipline of the Ironman 70.3 Pan Am championships, Panama is in a separate file - `panama_swim`, `panama_bike`, and `panama_run`. Create a similar map to the one you created with my cycling data. You will need to make some small changes: 1. combine the files (HINT: `bind_rows()`, 2. make the leading dot a different color depending on the event (for an extra challenge, make it a different image using `geom_image()!), 3. CHALLENGE (optional): color by speed, which you will need to compute on your own from the data. You can read Heather's race report [here](https://heatherlendway.com/2016/02/10/ironman-70-3-pan-american-championships-panama-race-report/). She is also in the Macalester Athletics [Hall of Fame](https://athletics.macalester.edu/honors/hall-of-fame/heather-lendway/184) and still has records at the pool. 
  

```r
panama_ironman <- panama_swim %>% 
bind_rows(panama_bike) %>%
  bind_rows(panama_run)

panama_map <- get_stamenmap(
  bbox = c(left = -79.61, bottom = 8.88,
           right = -79.45, top = 9.1),
  maptype = "terrain",
  zoom = 13)

panama_race <- ggmap(panama_map) +
  geom_path(data = panama_ironman , 
             aes(x =  lon, y = lat, color = event),
             size = 1.5) +
  geom_point(data = panama_ironman , 
             aes(x =  lon, y = lat, color = event),
             size = 1.5) +
  labs(title = "Panama Ironman Championships",
       color = "Event",
       x = "",
       y = "") +
  scale_color_manual(values = c("Swim" = "blue4",
                               "Bike" = "darkgoldenrod3",
                               "Run" = "red2")) +
  transition_reveal(time)
  theme_map() +
  theme(legend.background = element_blank())
  
animate(panama_race, nframes = 100,
        duration = 8, renderer = gifski_renderer())

anim_save("panama_race.gif")
```


```r
knitr::include_graphics("panama_race.gif")
```

![](panama_race.gif)<!-- -->
  
## COVID-19 data

  6. In this exercise, you are going to replicate many of the features in [this](https://aatishb.com/covidtrends/?region=US) visualization by Aitish Bhatia but include all US states. Requirements:
 * Create a new variable that computes the number of new cases in the past week (HINT: use the `lag()` function you've used in a previous set of exercises). Replace missing values with 0's using `replace_na()`.  
  * Filter the data to omit rows where the cumulative case counts are less than 20.  
  * Create a static plot with cumulative cases on the x-axis and new cases in the past 7 days on the y-axis. Connect the points for each state over time. HINTS: use `geom_path()` and add a `group` aesthetic.  Put the x and y axis on the log scale and make the tick labels look nice - `scales::comma` is one option. This plot will look pretty ugly as is.
  * Animate the plot to reveal the pattern by date. Display the date as the subtitle. Add a leading point to each state's line (`geom_point()`) and add the state name as a label (`geom_text()` - you should look at the `check_overlap` argument).  
  * Use the `animate()` function to have 200 frames in your animation and make it 30 seconds long. 
  * Comment on what you observe.
  

```r
covid_cases <- covid19 %>%
  group_by(state) %>%
  mutate(weekly_cases = replace_na(lag(cases,n = 7), 0)) %>%
  filter(weekly_cases >= 20) %>%
  ggplot(aes(x = cases, y = weekly_cases, 
             color = state, group = state)) +
  geom_path() +
  scale_x_log10(label = scales::comma) +
  scale_y_log10(label = scales::comma) +
  geom_point(size = 2) +
  geom_text(aes(label = state), check_overlap = TRUE) +
  transition_reveal(date) +
  labs(title = "Trajectory of US Covid-19 Confirmed Cases",
       subtitle = "Date: {frame_along}",
       y = "Weekly Cases",
       x = "Cases") +
  theme(legend.position = "none") 
  
  
animate(covid_cases, nframes = 200,
        duration = 30, renderer = gifski_renderer())

anim_save("covid.gif")
```
  

```r
knitr::include_graphics("covid.gif")
```

![](covid.gif)<!-- -->

> Originally New York has the most cases, but as time goes on, their level of cases gets surpassed by California and many southern states.
  
  7. In this exercise you will animate a map of the US, showing how cumulative COVID-19 cases per 10,000 residents has changed over time. This is similar to exercises 11 & 12 from the previous exercises, with the added animation! So, in the end, you should have something like the static map you made there, but animated over all the days. The code below gives the population estimates for each state and loads the `states_map` data. Here is a list of details you should include in the plot:
  
  * Put date in the subtitle.   
  * Because there are so many dates, you are going to only do the animation for all Fridays. So, use `wday()` to create a day of week variable and filter to all the Fridays.   
  * Use the `animate()` function to make the animation 200 frames instead of the default 100 and to pause for 10 frames on the end frame.   
  * Use `group = date` in `aes()`.   
  * Comment on what you see. 
  

```r
census_pop_est_2018 <- read_csv("https://www.dropbox.com/s/6txwv3b4ng7pepe/us_census_2018_state_pop_est.csv?dl=1") %>% 
  separate(state, into = c("dot","state"), extra = "merge") %>% 
  select(-dot) %>% 
  mutate(state = str_to_lower(state))

states_map <- map_data("state")
```
  

```r
states_map <- map_data("state")

covid_state_map <- covid19 %>%
  complete(state, date = seq.Date(min(date), max(date), by = "day")) %>%
  filter(wday(date, label = TRUE) == "Fri") %>%
  group_by(state) %>%
  mutate(state = str_to_lower(state)) %>%
  right_join(census_pop_est_2018,
            by = c("state")) %>% 
  mutate(cases_per_10000 = (cases/est_pop_2018)*10000) %>%
  ggplot +
  geom_map(map = states_map,
           aes(map_id = state,
               fill = cases_per_10000,
               group = date)) +
  expand_limits(x = states_map$long, y = states_map$lat) +
  theme_map() +
  labs(title = "Covid Cases per 10,000 Citizens",
       subtitle = "Date: {closest_state}",
       fill = "Cases") +
   scale_fill_gradient(low = "lightpink1", high = "violetred4", na.value = "thistle2")  +
  transition_states(date)
 
animate(covid_state_map, nframes = 200, duration = 10, renderer = gifski_renderer()) 

anim_save("covid_statemap.gif")
```


```r
knitr::include_graphics("covid_statemap.gif")
```

![](covid_statemap.gif)<!-- -->

> Originally almost all states had around the same amount of cases, but as time goes on, southern/Republican states tended to have more cases than northern/Democratic states
## Your first `shiny` app (for next week!)

NOT DUE THIS WEEK! If any of you want to work ahead, this will be on next week's exercises.

  8. This app will also use the COVID data. Make sure you load that data and all the libraries you need in the `app.R` file you create. Below, you will post a link to the app that you publish on shinyapps.io. You will create an app to compare states' cumulative number of COVID cases over time. The x-axis will be number of days since 20+ cases and the y-axis will be cumulative cases on the log scale (`scale_y_log10()`). We use number of days since 20+ cases on the x-axis so we can make better comparisons of the curve trajectories. You will have an input box where the user can choose which states to compare (`selectInput()`) and have a submit button to click once the user has chosen all states they're interested in comparing. The graph should display a different line for each state, with labels either on the graph or in a legend. Color can be used if needed. 
  
## GitHub link

  9. Below, provide a link to your GitHub page with this set of Weekly Exercises. Specifically, if the name of the file is 05_exercises.Rmd, provide a link to the 05_exercises.md file, which is the one that will be most readable on GitHub. If that file isn't very readable, then provide a link to your main GitHub page.

[GitHub Link](https://github.com/katiefunk01/homework_5)

**DID YOU REMEMBER TO UNCOMMENT THE OPTIONS AT THE TOP?**
