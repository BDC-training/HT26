_Course: VT26 R programming (SC00035)_
<hr>

## Background - Tidyverse
Tidyverse is a powerful set of tools used for data manipulation and visualization, including tools like `dplyr` and `ggplot2`. 
`dplyr` is used to manipulate data by filtering, sorting, and summarizing a dataset. More info at https://dplyr.tidyverse.org/

## Background - Data preparation and preprocessing
Data preparation and preprocessing is done to prepare data for further analytics/visualization.
Majority of the time is spent on this rather than analytics (about 50-80% of the time).
## Tidy data
Tidy data makes data manipulation, modelling and visualization easier.
Tidying your data means storing it in a consistent form. When your data is tidy, each variable is a column, and each row is an observation. 
Once you have tidy data, a common first step is to transform it. 
Transformation includes narrowing in on observations of interest (data from a certain year, all cars with a certain color), 
creating new columns that are functions of existing columns (like computing BMI from height and weight), and calculating summary statistics (like counts or means). Together, tidying and transforming are called wrangling.
## dplyr basics
Pipe operator `%>%`, passes object on left hand side right hand side `%>%` 

"Piping" with `%>%` makes code more readable, e.g.
```R
iris %>%
  group_by(Species) %>%
  summarise(mean_sw = mean(Sepal.Width)) %>%
  arrange(mean_sw)
```
This can be interpreted as use dataset `iris`, group it on `Species`, use the `summarise` function to calculate the mean `Sepal.Width` (column `mean_sw`) and arrange/sort it on the new column `mean_sw`. 

## dplyr verbs

`mutate()` adds new columns that are functions of existing columns.

`select()` keep columns based on their names.

`filter()` keep rows matching a condition.

`summarise()` reduces multiple values down to a single summary.

`arrange()` changes the ordering of the rows.

Other frequently used: `rename()`, `distinct()`, `drop_na()`, `left_join()` etc.


These all combine naturally with `group_by()` which allows you to perform any operation "by group".
If you need help just use the help function or simply `?`, for example `?mutate`. By scrolling down to the examples for the functions you have a good way to learn how to use the functions.

So lets start the first data wrangling with tidyverse excersise!
Good Luck\!

## Tidyverse: get started
First install and load the `tidyverse` package if you haven't yet.
```{R}
# install.packages("tidyverse")
library(tidyverse)
```

### Exercises
**E1.** Get to know the data in the built in dataset `mtcars` by using the following code
```{r}
?mtcars
head(mtcars)
summary(mtcars)
```
**E2.** Use the `filter` function to select all cars with 6 cylinders (column `cyl`) in the dataset `mtcars`. HINT: use `?filter` to see an example.
<details>
  <summary>Solution</summary>
  
  
  ```R
  mtcars %>% filter(cyl == 6)
  ```
</details>


**E3.** Use the `filter` function to select all cars with 6 cylinders (column `cyl`) that can drive more than 20 miles per gallon (column `mpg`) in the dataset `mtcars`.
<details>
  <summary>Solution</summary>
  
  
  ```R
  mtcars %>% filter(cyl == 6, mpg > 20)
  ```
</details>

**E4.** Use the `select` function to select column `cyl` and `gear` in the dataset `mtcars`. HINT: use `?select` to see an example.

<details>
  <summary>Solution</summary>
  
  
  ```R
  mtcars %>% select(cyl, gear)
  ```
</details>

**E5.** Use the `select` function to select the first five columns and the last column in the dataset `mtcars`. HINT: use the `names` function to display the column names and thereafter use `:` within the select function to select the five first columns. Example: `select(first_column_name:fifth_column_name, last_column_name)`
<details>
  <summary>Solution</summary>
  
  
  ```R
  names(mtcars)
  mtcars %>% select(mpg:drat, carb)
  ```
</details>

**E6.** One mpg corresponds to 23.52 liters per 10 km. Use the `mutate` function to transform `mpg` to a new column called `liter_per_10km` by dividing 23.52 with `mpg` in the dataset `mtcars`. HINT: use `?mutate` to see an example.
<details>
  <summary>Solution</summary>
  
  
  ```R
  mtcars %>% 
    mutate(liter_per_10km = 23.52/mpg)
  ```
</details>

**E7.** Use the following code to recode the numeric column am to a factor column called am_recoded where  0 = Automatic, 1 = Manual.

```R
mtcars %>% mutate(am_recoded = recode(am, "0" = "Automatic", "1" = "Manual"))
```

Now extract the newly created column `am_recoded` using `select` or `pull` and pipe it to `table` function to get a table displaying the number of cars that are automatic and manual
<details>
  <summary>Solution</summary>
  
  
  ```R
  mtcars %>% 
    mutate(am_recoded = recode(mtcars$am, "0" = "Automatic", "1" = "Manual")) %>% 
    select(am_recoded) %>% 
    table
  ```
</details>

**E8.** Use the `group_by` and `summarise` functions to calculate the mean horsepower (column `hp`) grouped by number of cylinders (column `cyl`) in the dataset `mtcars`. HINT: use `?group_by` and `?summarise` to see examples. And finally sort the data using `arrange(desc(mean_hp))`
<details>
  <summary>Solution</summary>
  
  
  ```R
  mtcars %>% 
    group_by(cyl) %>% 
    summarise(mean_hp = mean(hp)) %>%
    arrange(desc(mean_hp))
  ```
</details>


**E9.** Combine what you have learned so far: Use the `mutate` function to create a new column called liter_per_10km (as above) which is the ratio of 23.52 and `mpg` in the dataset `mtcars`.
Use the `group_by` and `summarise` functions to calculate the mean and max fuel consumption liter_per_10km grouped by number of cylinders (column `cyl`). What do you have left?
<details>
  <summary>Solution</summary>
  
  
  ```R
  mtcars %>%
    mutate(liter_per_10km = 23.52/mpg) %>% 
    group_by(cyl) %>%
    summarise(mean_liter_per_10km = mean(liter_per_10km),
              max_liter_per_10km = max(liter_per_10km))

  ```
</details>


**E10.** Remove duplicated rows in your dataset using the `distinct` function
First create duplicated sample data by running `test_data <- bind_rows(mtcars, mtcars)` which binds two identical datasets together.
Check number if rows in the data frame by using the command `nrow` and remove the duplicated rows by using the `distinct` function and use `nrow` again to check it worked. HINT: use `?distinct` to see an example. 

<details>
  <summary>Solution</summary>
  
  
  ```R
  test_data <- bind_rows(mtcars, mtcars)
  nrow(test_data)
  test_data <- test_data %>% distinct
  nrow(test_data)
  ```
</details>

**E11.** Basic imputation: first create some sample data with some missing values.
```R
df <- data.frame(A = c(2,NA,9,NA),                 
                 B = c(4,NA,55,66),                 
                 C = c(3,44,1,NA))
```
We are going to replace all NAs in column A with the median for column A using `mutate` and `replace` functions. This can be done using the following code, where you should replace the three dots with the correct column name.
```R
df %>% mutate(... = replace(..., is.na(...), median(..., na.rm = TRUE)))
```

To do this for all columns use `mutate` and the `across` function. This can be done using the following code, where you should replace the three dots with the correct column names.
```R
df %>% mutate(across(..., ~replace(., is.na(.), median(., na.rm=TRUE))))
```

<details>
  <summary>Solution</summary>
  
  
  ```R
  df %>% mutate(A = replace(A, is.na(A), median(A, na.rm = TRUE)))
  df %>% mutate(across(A:C, ~replace(., is.na(.), median(., na.rm=TRUE))))
  ```
</details>


## Part 2
**E1.** Start by reading in the data. Get to know the data by using the following code
```{r}
df1 <- read_tsv("https://raw.githubusercontent.com/bcfgothenburg/Hands-on/master/Patient_data.txt")
df2 <- read_tsv("https://raw.githubusercontent.com/bcfgothenburg/Hands-on/master/Meta_data.txt")

head(df1)
head(df2)
summary(df1)
summary(df2)
```
**E2.** Mutating joins add columns from y to x, matching observations based on the keys. There are four mutating joins: the inner join, and the three outer joins (left_join, right_join, full_join). We are going to perform a `left_join` that keeps all observations in the first data frame. Joins corresponds to the `merge` function in base R. We will join df1 with df2 by both id and sex to create a dataset called df_joined. Note that the sex variable is called gender in df2. Sometimes you get data with non-unique id:s but in this case the combination of id and sex is unique. Therefore, we have to specify within the by argument that `"sex"="gender"`. HINT: Type `?left_join` in console to learn more.
<details>
  <summary>Solution</summary>
  
  
  ```R
  df_joined <- df1 %>%
    left_join(df2, 
              by=c("id"="id", "sex"="gender"))
  ```
</details>


**E3.** Use the the newly created df_joined to make a boxplot by piping the data to ggplot. Plot insuline levels (lab_ins) grouped by diabetes status (clin_diabetes).
<details>
  <summary>Solution</summary>
  
  
  ```R
  df_joined %>%
    ggplot(aes(factor(clin_diabetes), lab_ins)) +
    geom_boxplot() +
    theme_classic()
  ```
</details>

**E4.** Do E2-E3 in one step without saving to the object df_joined

<details>
  <summary>Solution</summary>
  
  
  ```R
  df1 %>%
    left_join(df2, 
              by=c("id"="id", "sex"="gender")) %>%
    ggplot(aes(factor(clin_diabetes), lab_ins)) +
    geom_boxplot() +
    theme_classic()
  ```
</details>

**E5.** Use the `select` function to select only the numeric columns in `df_joined` by using the predicate function `where` within the `select` function. HINT: Type `?tidyselect::where` in console to see an example. 
<details>
  <summary>Solution</summary>
  
  
  ```R
  df_joined %>%
    select(where(is.numeric))
  ```
</details>

**E6.** On all variables selected in E5 we now want to calculate the mean and sd by piping the previous step to the summary function `summarise`. To calculate the mean and sd of all variables we have to use the `across` together with the tidy-select function everything(). HINT:  Type `?across` in console to learn more and to see an example.

<details>
  <summary>Hint E6</summary>
  
  
  ```R
  summarise(across(everything(), list(mean=mean, sd=sd)))
  ```
</details>

<details>
  <summary>Solution</summary>
  
  
  ```R
  df_joined %>%
    select(where(is.numeric)) %>%
    summarise(across(everything(), list(mean=mean, sd=sd)))
  ```
</details>

**E7.** Can we solve E6 in a more intuitive and shorter way? By replacing everything() with where(is.numeric)) you can omit the select step on the second line

<details>
  <summary>Solution</summary>
  
  
  ```R
  df_joined %>%
    summarise(across(where(is.numeric), list(mean=mean, sd=sd)))
  ```
</details>

**E8.** Tidy data makes data manipulation, modelling and visualization easier. When your data is tidy, each variable is a column, and each row is an observation. We will now practice transforming data in wide format to the tidy long format. Start by reading in the data and get to the know the data using the following code
 
  ```R
  dfw <- read_tsv("https://raw.githubusercontent.com/bcfgothenburg/Hands-on/master/Weight_data.txt")
  head(dfw)
  ```



**E9.** To create long format data for the three weight variables in the dfw dataset, pipe dfw object to the `pivot_longer` function and specify the cols, names_to and values_to arguments. HINT: Type `?pivot_longer` to see an example.
<details>
  <summary>Solution</summary>
  
  
  ```R

  dfw %>%
    pivot_longer(cols = Weight_1yr:Weight_3yr, names_to="Weight", values_to="kg")
  ```
</details>


**E10.** The Weight column contains information about timepoints which can be extracted using the function `parse_number`. Use `mutate` to create a new column called timepoint by setting `timepoint = parse_number(Weight)` within the mutate call. 
<details>
  <summary>Solution</summary>
  
  
  ```R
  dfw %>%
    pivot_longer(cols = Weight_1yr:Weight_3yr, names_to="Weight", values_to="kg") %>%
    mutate(timepoint = parse_number(Weight))
  ```
</details>

**E11.** Now we have data in suitable format for visualization. Pipe the line of code from E10 to ggplot() and specify the aes argument to `aes(timepoint, kg, col=factor(ID), group=factor(ID))` and add the layers `geom_point()` and `geom_line()`

<details>
  <summary>Solution</summary>
  
  
  ```R
    dfw %>%
      pivot_longer(cols = Weight_1yr:Weight_3yr, names_to="Weight", values_to="kg") %>%
      mutate(timepoint = parse_number(Weight)) %>%
      ggplot(aes(timepoint, kg, col=factor(ID), group=factor(ID))) +
      geom_point() + 
      geom_line() +
      theme_classic()
  ```
</details>

**E12.** If we want to plot each individual separately, we can use faceting which creates small multiples each showing a different subset of the data. This is achieved by piping adding the layer `facet_wrap(vars(ID))` to the previous code

<details>
  <summary>Solution</summary>
  
  
  ```R
    dfw %>%
      pivot_longer(cols = Weight_1yr:Weight_3yr, names_to="Weight", values_to="kg") %>%
      mutate(timepoint = parse_number(Weight)) %>%
      ggplot(aes(timepoint, kg, col=factor(ID), group=factor(ID))) +
      geom_point() + 
      geom_line() +
      theme_classic() +
      facet_wrap(vars(ID))
  ```
</details>

Mastering the tidyverse toolbox will help you to do advanced data manipulations and conduct rigorous exploratory data analysis of future data!

***
### Home: [R programming](https://github.com/BDC-training/VT26/blob/main/README.md) 
***

_Developed by Björn Andersson and Jari Martikainen 2024_