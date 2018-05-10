ggplot2
========================================================
author: Richel Bilderbeek
date: 2018-05-09
autosize: true



[https://github.com/richelbilderbeek/Science](https://github.com/richelbilderbeek/Science)  ![CC-BY-NC-SA](CC-BY-NC-SA.png)

![RuG and GELIFES and TECE logo](footer.png)





Desire
========================================================
title: false
![](meme_desire.jpg)

Non-goal
========================================================
![](ggplot2_examples.png)

<!-- I will give many examples to demonstrate ggplot2 -->
<!-- One can use any search engine to do that -->

Goal
========================================================
![ggplot2 logo](ggplot2_logo.png)
***
- Why use `ggplot2`
- Philosophy behind `ggplot2`
- Incorrect use of `ggplot2`
- Correct use of `ggplot2`


```r
library(ggplot2)
```

<!-- This talk is about what one cannot find on Stack Overflow -->
<!-- and prevents needing to go there -->

Why use ggplot2?
========================================================

 * A grammar of graphics to express oneself in
 * Forced to work tidily
 * Recommended for teaching

***
![](meme_r_plot.jpg)

Philosophy
========================================================
![Leland Wilkinson](leland_wilkinson.jpg)
***
![Grammar of Graphics](grammar_of_graphics_book.jpg)

<!-- 'Grammar of Graphics' came out in 2005 -->

Philosophy
========================================================

![Grammar of Graphics](grammar_of_graphics.png)

<!-- 'Grammar of Graphics' has an idea behind it -->

Philosophy
========================================================
![Hadley Wickham](hadley_wickham.jpg)
***
 * Grammar of Graphics
 * Tidy Data

![R for Data Science](r_for_data_science.png)
*Wickham & Grolemund. R for data science. 2016*

<!-- Wickham: most time is spent tidying data -->

Frequency data
========================================================

![](meme_normal_distribution.jpg)

Messy data
========================================================


```r
messy_df <- data.frame(
  matrix(rnorm(n = 1000), ncol = 1000)
)
colnames(messy_df) <- paste0(
  "z", seq(1, 1000)
)
knitr::kable(messy_df[, 1:6])
```



|        z1|        z2|        z3|         z4|        z5|        z6|
|---------:|---------:|---------:|----------:|---------:|---------:|
| 0.6539183| 0.0190523| -1.849504| -0.1327633| -1.198818| -1.329741|

Plotting messy data is easy without ggplot2
========================================================


```r
hist(t(messy_df))
```

![plot of chunk unnamed-chunk-6](ggplot2-figure/unnamed-chunk-6-1.png)

Plotting messy data is hard in ggplot2
========================================================


```r
ggplot(
  data.frame(z = t(messy_df[1, ])[, 1]),
  aes(z)
) + geom_histogram()
```

![plot of chunk unnamed-chunk-7](ggplot2-figure/unnamed-chunk-7-1.png)

<!-- if data is messy, a ggplot2 call will look ugly  -->

Tidy Data
========================================================
![tidyr logo](tidyr_logo.png)
***
 * One measurement per row, 'long form'
 * Factors as factors


```r
library(tidyr)
```


Wrangling messy data to Tidy Data
========================================================


```r
df <- gather(messy_df, "replicate", "value")
knitr::kable(df[1:6, ])
```



|replicate |      value|
|:---------|----------:|
|z1        |  0.6539183|
|z2        |  0.0190523|
|z3        | -1.8495040|
|z4        | -0.1327633|
|z5        | -1.1988182|
|z6        | -1.3297415|

Plotting Tidy Data is easy
========================================================


```r
ggplot(df, aes(value)) +
  geom_histogram()
```

![plot of chunk unnamed-chunk-10](ggplot2-figure/unnamed-chunk-10-1.png)

Plotting Tidy Data is easy
========================================================


```r
ggplot(df, aes(value)) +
  geom_density()
```

![plot of chunk unnamed-chunk-11](ggplot2-figure/unnamed-chunk-11-1.png)

Plotting Tidy Data is easy
========================================================


```r
ggplot(df, aes(x = "", y = value)) +
  geom_boxplot()
```

![plot of chunk unnamed-chunk-12](ggplot2-figure/unnamed-chunk-12-1.png)

Plotting Tidy Data is easy
========================================================


```r
ggplot(df, aes(x = "", y = value)) +
  geom_violin()
```

![plot of chunk unnamed-chunk-13](ggplot2-figure/unnamed-chunk-13-1.png)

Messy data
========================================================


```r
messy_df <- data.frame(
  matrix(
    rnorm(n = 2002), ncol = 1001, nrow = 2
  )
)
colnames(messy_df) <- c(
  "treatment", paste0("z", seq(1, 1000))
)
messy_df[1, 1] <- "A"
messy_df[2, 1] <- "B"
knitr::kable(messy_df[, 1:6])
```



|treatment |         z1|        z2|         z3|        z4|         z5|
|:---------|----------:|---------:|----------:|---------:|----------:|
|A         | -0.1836515| 1.9340660| -0.8871935| 0.2657320|  0.1569296|
|B         |  0.0887142| 0.0827711|  0.5865041| 0.6194539| -1.0828811|

Plotting messy data is easy without ggplot2
========================================================


```r
hist(as.numeric(messy_df[1, 2:1001]))
hist(
  as.numeric(messy_df[2, 2:1001]), 
  add = TRUE
)
```

![plot of chunk unnamed-chunk-15](ggplot2-figure/unnamed-chunk-15-1.png)

<!-- Note: the reason I do not use a stacked histogram, -->
<!-- is because there is no easy way without ggplot2 -->

Plotting messy data is hard in ggplot2
========================================================


```r
ggplot(
  data.frame(
    treatment = rep(
      messy_df$treatment, each = 1000
    ),
    z = c(
      t(messy_df[1, 2:1001])[, 1], 
      t(messy_df[2, 2:1001])[, 1]
    )
  ),
  aes(z, fill = treatment)
) + geom_histogram(position = "identity")
```

![plot of chunk unnamed-chunk-16](ggplot2-figure/unnamed-chunk-16-1.png)

<!-- if data is messy, a ggplot2 call will look ugly  -->

Wrangling messy data to Tidy Data
========================================================


```r
df <- gather(
  messy_df, "replicate", "value", z1:z1000
)
knitr::kable(df[1:6, ])
```



|treatment |replicate |      value|
|:---------|:---------|----------:|
|A         |z1        | -0.1836515|
|B         |z1        |  0.0887142|
|A         |z2        |  1.9340660|
|B         |z2        |  0.0827711|
|A         |z3        | -0.8871935|
|B         |z3        |  0.5865041|

Plotting Tidy Data is easy
========================================================


```r
ggplot(
  df, aes(x = value, fill = treatment)
) + geom_histogram(position = "identity")
```

![plot of chunk unnamed-chunk-18](ggplot2-figure/unnamed-chunk-18-1.png)

Plotting Tidy Data is easy
========================================================


```r
ggplot(
  df, aes(x = value, fill = treatment)
) + geom_density()
```

![plot of chunk unnamed-chunk-19](ggplot2-figure/unnamed-chunk-19-1.png)

Plotting Tidy Data is easy
========================================================


```r
ggplot(
  df, aes(x = treatment, y = value)
) + geom_boxplot()
```

![plot of chunk unnamed-chunk-20](ggplot2-figure/unnamed-chunk-20-1.png)

Plotting Tidy Data is easy
========================================================


```r
ggplot(
  df, aes(x = treatment, y = value)
) + geom_violin()
```

![plot of chunk unnamed-chunk-21](ggplot2-figure/unnamed-chunk-21-1.png)

Messy data
========================================================


```r
messy_df <- data.frame(
  treatment = rep(c(1, 2), times = 1000),
  value = rnorm(n = 2000)
)
knitr::kable(head(messy_df))
```



| treatment|      value|
|---------:|----------:|
|         1| -1.7130498|
|         2|  0.8466842|
|         1|  1.1075262|
|         2|  0.2018014|
|         1| -0.1251446|
|         2| -0.2059191|

<!-- messy data: treatment is not a factor  -->

Plotting messy data is easy without ggplot2
========================================================


```r
hist(messy_df[messy_df$treatment == 1, 2])
hist(
  messy_df[messy_df$treatment == 2, 2], 
  add = TRUE
)
```

![plot of chunk unnamed-chunk-23](ggplot2-figure/unnamed-chunk-23-1.png)

Plotting messy data goes wrong in ggplot2
========================================================


```r
ggplot(
  messy_df, 
  aes(x = value, fill = treatment)
) + geom_histogram(position = "identity")
```

![plot of chunk unnamed-chunk-24](ggplot2-figure/unnamed-chunk-24-1.png)

<!-- Does not seperate by treatment, would confuse a beginner -->

Plotting messy data is hard in ggplot2
========================================================


```r
ggplot(position = "identity") + 
  geom_histogram(
    data = messy_df[
      messy_df$treatment == 1, 
    ], 
    aes(value), fill = "red"
  ) + 
  geom_histogram(
    data = messy_df[
      messy_df$treatment == 2, 
    ], 
    aes(value), fill = "blue"
  )
```

![plot of chunk unnamed-chunk-25](ggplot2-figure/unnamed-chunk-25-1.png)

<!-- if data is messy, a ggplot2 call will look ugly  -->

Wrangling messy data to Tidy Data
========================================================


```r
df <- messy_df
df$treatment <- as.factor(df$treatment)
knitr::kable(df[1:6, ])
```



|treatment |      value|
|:---------|----------:|
|1         | -1.7130498|
|2         |  0.8466842|
|1         |  1.1075262|
|2         |  0.2018014|
|1         | -0.1251446|
|2         | -0.2059191|

Plotting Tidy Data is easy
========================================================


```r
ggplot(
  df, 
  aes(x = value, fill = treatment)
) + geom_histogram(position = "identity")
```

![plot of chunk unnamed-chunk-27](ggplot2-figure/unnamed-chunk-27-1.png)

Plotting Tidy Data is easy
========================================================


```r
ggplot(
  df, 
  aes(x = value, fill = treatment)
) + geom_density()
```

![plot of chunk unnamed-chunk-28](ggplot2-figure/unnamed-chunk-28-1.png)

Plotting Tidy Data is easy
========================================================


```r
ggplot(
  df, 
  aes(x = treatment, y = value)
) + geom_boxplot()
```

![plot of chunk unnamed-chunk-29](ggplot2-figure/unnamed-chunk-29-1.png)

Plotting Tidy Data is easy
========================================================


```r
ggplot(
  df, 
  aes(x = treatment, y = value)
) + geom_violin()
```

![plot of chunk unnamed-chunk-30](ggplot2-figure/unnamed-chunk-30-1.png)

Conclusions
========================================================

 * It is hard to plot messy data in `ggplot2`
 * It is easy to plot Tidy Data in `ggplot2`
 * Use Tidy Data, don't forget factors

Questions?
========================================================

![](meme_plot_messy_data.jpg)

[https://github.com/richelbilderbeek/Science](https://github.com/richelbilderbeek/Science)  ![CC-BY-NC-SA](CC-BY-NC-SA.png)


