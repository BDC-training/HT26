_Course: VT26 R programming (SC00035)_
<hr>

# ggplot basics

## Install packages

1. In this exercise we will work mainly with the R package ggplot2. Start by loading the `tidyverse` collection of packages which contains `ggplot2` 

```R
library(tidyverse)
```

2. During this exercise we will work with the data set `PimaIndiansDiabetes2`. This data can be downloaded from our github page with the code below. Spend a few minutes to get familiar with the data set using the `head()` function and the `str()` function

```R
PimaIndiansDiabetes2 <- read_tsv("https://raw.githubusercontent.com/bcfgothenburg/VT24/main/PimaIndiansDiabetes2.txt")

head(PimaIndiansDiabetes2)
```

## Create a ggplot

3.  Type the below line:
    ```R
    PimaIndiansDiabetes2 %>%
      ggplot
    ```

what is created by this line of code?

4. Now add aesthetics for `glucose` for ``x`` and `pressure` for ``y``, _**what changed?**_

<details>
  <summary>Solution</summary>
  
  
  ```R
  PimaIndiansDiabetes2 %>%
    ggplot(aes(x = glucose, fill = diabetes))

  # a plotting area appears without any data points
  ```
</details>

## Histograms

5.  The `PimaIndians2` data set has nine columns, Use the `ggplot()`
    function with only the column `glucose` as input and make a histogram. _**Which 
``geom`` layer do you need to make a histogram?**_

<details>
  <summary>Solution</summary>
  
  
  ```R
  PimaIndiansDiabetes2 %>%
    ggplot(aes(x = glucose)) +
    geom_histogram() 

  # geom_histogram() is the geom layer used for displaying histograms 
  ```
</details>

6. make another histogram where you color the bars by diabetes status (using the aes `fill`). Within the `geom` for histogram, specify `alpha=0.3`, **_what happens when you do this?_**

<details>
  <summary>Solution</summary>
  
  
  ```R
  PimaIndiansDiabetes2 %>%
    ggplot(aes(x = glucose, fill = diabetes)) +
    geom_histogram(aes(alpha = 0.3)) 
  ```
</details>


7. Next, instead of a histogram, make a density plot using `geom_density`, use the same setting for `alpha`.

<details>
  <summary>Solution</summary>
  
  
  ```R
  PimaIndiansDiabetes2 %>%
    ggplot(aes(x = glucose, fill = diabetes)) +
    geom_density(alpha = 0.3)
  ```
</details>


## Scatterplots

8. Now make a scatterplot with `ggplot`, give the `aes()` function two vectors as argument instead: `glucose` on the x-axis and `pressure` on the y-axis.

<details>
  <summary>Solution</summary>


  ```R
  PimaIndiansDiabetes2 %>%
    ggplot(aes(x = glucose, y = pressure)) + 
    geom_point()
  ```
</details>

9. The plot function uses the column names from the data frame as labels for the axis. Usually you want to write something that is more informative here. Add the layers `xlab` and `ylab` to your plot code, to set the labels to "Plasma Glucose Concentration" and "Diastolic blood pressure(mm Hg)" instead.

<details>
  <summary>Solution</summary>


  ```R
  PimaIndiansDiabetes2 %>%
    ggplot(aes(x = glucose, y = pressure)) + 
    geom_point() +
    xlab("Plasma Glucose Concentration") +
    ylab("Diastolic blood pressure(mm Hg)")
  ```
</details>

10. You can also add a title to the plot using the `ggtitle` layer. Add the layer `ggtitle()` to your plot code, to give your plot the header: "Scatter plot of Pima Indians data".

<details>
  <summary>Solution</summary>


  ```R
    PimaIndiansDiabetes2 %>%
      ggplot(aes(x = glucose, y = pressure)) + 
      geom_point() +
      xlab("Plasma Glucose Concentration") +
      ylab("Diastolic blood pressure(mm Hg)") +
      ggtitle("Scatter plot of Pima Indians data")
  ```
</details>

11. Next, color the points by if the subjects have diabetes or not.

<details>
  <summary>Solution</summary>


  ```R
    PimaIndiansDiabetes2 %>%
      ggplot(aes(x = glucose, y = pressure, color = diabetes)) + 
      geom_point() +
      xlab("Plasma Glucose Concentration") +
      ylab("Diastolic blood pressure(mm Hg)") +
      ggtitle("Scatter plot of Pima Indians data")
  ```
</details>

 12. Now instead of color by diabetes status, make the plot with facets 

<details>
  <summary>Solution</summary>


  ```R
   PimaIndiansDiabetes2 %>%
    ggplot(aes(x = glucose, y = pressure, color = diabetes)) + 
    geom_point() +
    xlab("Plasma Glucose Concentration") +
    ylab("Diastolic blood pressure(mm Hg)") +
    ggtitle("Scatter plot of Pima Indians data") +
    facet_grid(~ diabetes)
  ```
</details>

## Barplots 

A barplot is a staples diagram that shows the number of elements in different groups. We will create a barplot that shows how many individuals that have a blood pressure larger than 90, and how many are below.

13. Use the package `tidyverse` and the function `mutate()` to calculate which
    of the subjects have high vs low blood pressure as mentioned above,
    create a new variable named `HighBP`. Then create a barplot of this
    variable with ggplot.

<details>
  <summary>Solution</summary>


  ```R
  PimaIndiansDiabetes2 %>% 
    mutate(HighBP = pressure > 90) %>% 
    filter(!is.na(HighBP)) %>% 
    ggplot(aes(x = HighBP)) + 
    geom_bar()
  ```
</details>

## Boxplot 

14. Make a boxplot of glucose, grouped by diabetes status, color the boxes by diabetes status.

<details>
  <summary>Solution</summary>


  ```R
  PimaIndiansDiabetes2 %>%
  ggplot(aes(x = diabetes,  y = glucose, fill = diabetes)) +
    geom_boxplot()
  ```
</details>

15. Install and load the R package `ggsignif`. Next add the geom
    `geom_signif()` to add a p-value for comparing the glucose level of
    the diabetics to the glucose level of the non-diabetics, by using a
    Mann-Whitney or Wilcoxon test. Check the help of `geom_signif()` for
    details. **_Is there a significant difference between the groups?_**

<details>
  <summary>Solution</summary>


  ```R
  install.packages("ggsignif")
  library(ggsignif)

  PimaIndiansDiabetes2 %>% 
    ggplot(aes(x = diabetes, y = glucose, fill = diabetes)) +
    geom_boxplot() +
    geom_signif(comparisons = list(c("neg", "pos")), 
                map_signif_level = FALSE, 
                test="wilcox.test")
  ```
</details>

## Heatmaps

16. In this exercise we will use another dataset containing normalized read counts from a metagenomic investigation of microbial diversity. The data is available at https://raw.githubusercontent.com/bcfgothenburg/VT24/main/mine-data.csv. Read the data into R using a suitable function. Check the properties of the datasets using e.g. `str()`.

<details>
  <summary>Solution</summary>


  ```R
  mine_data <- read_csv("https://raw.githubusercontent.com/bcfgothenburg/VT24/main/mine-data.csv")
  str(mine_data)
  ```
</details>

17. Next, we will use the function `pivot_longer()`, included in the
    `tidyr` package (and hence also the `tidyverse` package), to change
    the format of the dataset from a wide to a long format. Instead of
    one column for each bacteria, we want one column with bacteria name
    and one column with the values of each measurement. Check the R help or online resources for information on how this function is used.

<details>
  <summary>Solution</summary>


  ```R
  mine_long <- mine_data %>% 
    pivot_longer(cols = Actinobacteria:Gammaproteobacteria, 
      names_to = "Class", 
      values_to = "Abundance")
  ```
</details>


18. Now we will make a heatmap using `ggplot`. For this we use the geom
    `geom_tile`, put sample names on the x-axis and bacteria class on
    the y-axis. Fill the tiles based on the abundance values.

<details>
  <summary>Solution</summary>


  ```R
  mine_long %>%
    ggplot(aes(x = Sample.name, y = Class, fill = Abundance)) +
    geom_tile() +
    xlab(label = "Sample")
  ```
</details>

19. The variable `Depth` has 3 different values. Now make the heatmap into a faceted using `facet_grid` 

<details>
  <summary>Solution</summary>


  ```R
  mine_long %>%
    ggplot(aes(x = Sample.name, y = Class, fill = Abundance)) +
    geom_tile() +
    xlab(label = "Sample") +
    facet_grid(~ Depth)
  ```
</details>

20. In the faceted heatmap you see that you have some empty columns, because those don't have values in those groups. One way of only showing the columns that contain values is to add the parameters `scales = "free", space="free"` to the `facet_grid()` layer.

<details>
  <summary>Solution</summary>


  ```R
  mine_long %>%
    ggplot(aes(x = Sample.name, y = Class, fill = Abundance)) +
    geom_tile() +
    xlab(label = "Sample") +
    facet_grid(~ Depth, scales = "free", space="free")
  ```
</details>

21. Make a transformation of the Abundance values by taking the square root, using the function `sqrt()`. Repeat the heatmap above by using the transformed values instead. 

<details>
  <summary>Solution</summary>


  ```R
  mine_long %>%
    mutate(Sqrt.abundance = sqrt(Abundance)) %>%
    ggplot(aes(x = Sample.name, y = Class, fill = Sqrt.abundance)) +
    geom_tile() +
    xlab(label = "Sample") +
    facet_grid(~ Depth, scales = "free", space="free")
  ```
</details>

22. Now also add the color scale in the heatmap using `scale_fill_gradient()`, define the parameters `low = "#FFFFFF", high = "#012345"` so the low values will be white and the high values will be dark blue.

<details>
  <summary>Solution</summary>


  ```R
  mine_long %>%
    mutate(Sqrt.abundance = sqrt(Abundance)) %>%
    ggplot(aes(x = Sample.name, y = Class, fill = Sqrt.abundance)) +
    geom_tile() +
    xlab(label = "Sample") +
    facet_grid(~ Depth, scales = "free", space="free") +
    scale_fill_gradient(name = "Sqrt(Abundance)",
                        low = "#FFFFFF",
                        high = "#012345")
  ```
</details>

23. As often in R instead of doing a lot of manual steps, there is a package available to make life easier. Now instead of using `ggplot` we will use the package `pheatmap` as was previously shown in the demo. First install and load the `pheatmap` package. Extract the columns with bacteria abundance from the original dataset, transpose the new matrix and transform the values with a `sqrt()`.

<details>
  <summary>Solution</summary>


  ```R
  install.packages("pheatmap")
  library(pheatmap)
  tmp <- mine_data %>% select(Actinobacteria:Gammaproteobacteria) %>% t %>% sqrt
  ```
</details>

24. The new matrix has samples as columns and bacterias as rows. Now use the column `Sample.name` in the original data.frame to set the column names in the new matrix.

<details>
  <summary>Solution</summary>


  ```R
  colnames(tmp) <- mine_data$Sample.name
  ```
</details>

25. Create a heatmap of this new matrix using the function `pheatmap()`. 

<details>
  <summary>Solution</summary>


  ```R
  pheatmap(tmp)
  ```
</details> 

26. We might want to add some annotation to the plot. Create a data.frame with one column named `Depth` containing values from the column `Depth` in the original data.frame. Also set the rownames of your annotation data.frame to the values in the column `Sample.name`

<details>
  <summary>Solution</summary>


  ```R
  annocol <- data.frame(Depth = factor(mine_data$Depth))
  rownames(annocol) <- mine_data$Sample.name
  ```
</details>

27. Now make a new heatmap adding the column annotation using the `annotation_col` argument in `pheatmap()`.

<details>
  <summary>Solution</summary>


  ```R
  pheatmap(tmp, annotation_col=annocol)
  ```
</details>


# Principal Component Analysis (PCA)
Now we will do a PCA plot of the same data. To do that we need to have bacterias as columns and samples as rows, and still have the `sqrt`-transformation so you can create the same tmp object as in the pheatmap exercise above but without the transpose step.

28. To start we need to calculate the principal components. This is done in a function called `prcomp()`. This function takes a numerical data frame as input. Calculate the prinicipal components for the numerical columns in the data using the `prcomp()` function. Store them in a variable called `pc`.

<details>
  <summary>Solution</summary>


  ```R
  tmp <- mine_data[,-(1:3)] %>% sqrt
  pc <- prcomp(tmp)
  ```
</details>

29. Plot your ``pc`` variable with the plot function. Set ``type="b"``. This plots the variances of the principal components. You can see that the variance is large for the first PC and decreases for every PC.

<details>
  <summary>Solution</summary>


  ```R
  plot(pc, type="b")
  ```
</details>

30. Your ``pc`` variable contains a lot of information. It is neither a
vector, a data frame, a matrix or any of the other classes we have seen
so far.

    1. Write ``class(pc)`` in your console to find out which class `pc` belongs to.

    2. Write ``names(pc)`` in your console to find out what `pc` contains.

31. The prinicpal components are found in the matrix `pc$x`. We can plot
    them in a score plot.

    1.  Take a look at the matrix `pc$x` using `head()` to find out how
        the principal components are stored.
    2.  Next, convert the matrix `pc$x` into a `data.frame`.
    3.  Then use this `data.frame` to create a scatter plot, with `PC1`
        on the x-axis and `PC2` on the y-axis, using `ggplot()`
    4.  To be able to distinguish the different depths, add the col
        aestetic with the `mine.data$Depth` vector as input, you might
        need to convert the vector into a factor.

<details>
  <summary>Solution</summary>


  ```R
  pc$x %>% 
  as.data.frame() %>% 
  ggplot(aes(x=PC1, y=PC2, color=as.factor(mine_data$Depth)))+ 
    geom_point()
  ```
</details>

32. Change the theme of the PCA plot to `theme_classic` by adding the layer `theme_classic()`

<details>
  <summary>Solution</summary>


  ```R
  pc$x %>% 
  as.data.frame() %>% 
  ggplot(aes(x=PC1, y=PC2, color=as.factor(mine_data$Depth)))+ 
    geom_point() +
    theme_classic()
  ```
</details>


***
### Home: [R programming](https://github.com/BDC-training/VT26/blob/main/README.md) 
***

_Developed by Maria Nethander, 2019. Modified by Malin Östensson, 2020. Modified by Björn Andersson and Jari Martikainen, 2024_