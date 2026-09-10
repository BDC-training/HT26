_Course: VT26 R programming (SC00035)_
<hr>


# Tests comparing two groups

## Dataset

Here, we will use the built-in R data set named `ToothGrowth` that contains data from an experiment which was used to investigate if vitamin C delivery method has an effect on tooth growth. _The
response is the length of odontoblasts (cells responsible for tooth
growth) in 60 guinea pigs. Each animal received one of three dose levels
of vitamin C (0.5, 1, and 2 mg/day) by one of two delivery methods,
orange juice or ascorbic acid (a form of vitamin C and coded as VC)._

1. Start by assigning the object `ToothGrowth` to a new variable called
`my_data`

<details>
  <summary>Solution</summary>
  
  
  ```R
  my_data <- ToothGrowth
  ```
</details>

## Examine the data

2. Some statistical tests assumes that the data is normally distributed i.e. t-test and the ANOVA test. To check this visually you can create a histogram and/or a qqplot. First, create a histogram of the length variable `len` in the data frame `my_data` to check if the data follows the [normal distribution](https://en.wikipedia.org/wiki/Normal_distribution) or not. Another way to check if the data is normally distributed is to use the `qqnorm` function by comparing the variable `len` with a normal distribution. HINT: Use the functions `qqnorm` followed by `qqline` on the variable `my_data$len`

<details>
  <summary>Solution</summary>
  
  
  ```R
  hist(my_data$len)
  qqnorm(my_data$len)
  qqline(my_data$len)
  ```
</details>

## Check normality

3. We can test the normality assumption using a statistical test. Now use the `shapiro.test()` function to test if the variable `my_data$len` is normally distributed. The test rejects the null hypothesis of normality when the p-value is less than or equal to the significance level of 0.05. **_Is it suitable to assume normal distribution when you analyze this dataset?_**

<details>
  <summary>Solution</summary>
  
  
  ```R
  shapiro.test(my_data$len)

  # p-value = 0.1091 which is above the significance level of 0.05 and therefore one can assume that the data follows a normal distribution.
  ```
</details>

## Visualize Data

We are going to compare the length of odontoblasts in guinea pigs which had their vitamin C delivered by orange juice (OJ) to those which had their vitamin C from ascorbic acid (VC). That is, we want to see if the variable `my_data$len` depends on the variable `my_data$supp`. We can write the relation as an R formula: `my_data$len ~ my_data$supp`.

4. Using the formula above and the `boxplot()` function, create a boxplot of the length variable `my_data$len` split into groups according to the supplement type `my_data$supp`

<details>
  <summary>Solution</summary>
  
  
  ```R
  boxplot(my_data$len ~ my_data$supp)
  ```
</details>

## Two sample test

5. Based on the result of your previous check of normality choose to make either a t-test or a wilcoxon test. Test if the mean length of odontoblasts are equal for both supplements OJ and VC.

   Hint: You can use the formula `my_data$len ~ my_data$supp` within the test function.

<details>
  <summary>Solution</summary>
  
  
  ```R
  t.test(my_data$len ~ my_data$supp)
  # p-value = 0.06063, hence the difference in means is not statistically significant (at the significance level 0.05)
  ```
</details>

## Power of a two-sample t-test

Here we will demonstrate how to conduct a power calculation for a two-sample t-test. We will use the `pwr.t.test()` function from the `pwr` package. See the [vignette](https://cran.r-project.org/web/packages/pwr/vignettes/pwr-vignette.html) to learn more and for examples of how to use the package. Read the first paragraph in which the author describes the basic idea on how all of the functions in the `pwr` package work. Without diving too much into details, we will calculate what sample size is needed to detect a medium [effect size](https://en.wikipedia.org/wiki/Effect_size) of `d=0.5` at the significance level 0.05 and with power 0.80.

  
  ```R
  install.packages("pwr")
  library(pwr)

  pwr.t.test(
    d = 0.5, 
    power = 0.8,
    sig.level = 0.05, 
    type = "two.sample", 
    alternative = "two.sided"
  )
  ```

  The function tells us we should have 63.76561 samples in each group, which we round up to 64.

  6. Now create a sample size vector containing all numbers that are multiples of 5 between 5 and 100 (i.e 5, 10, 15, ..., 100) by defining the object `sample_size <- seq(5, 100, by = 5)`. Then call the `pwr.t.test()` function with the arguments `d = 0.5`, `n = sample_size`, `sig.level = 0.05`, `type = "two.sample"`, `alternative = "two.sided"` and store the result in an object named `power_data`


<details>
  <summary>Solution</summary>
  
  
  ```R
  sample_size <- seq(5, 100, by = 5)

  power_data <- pwr.t.test(
    d = 0.5, 
    n = sample_size, 
    sig.level = 0.05, 
    type = "two.sample", 
    alternative = "two.sided"
  )

  power_data # contains the power for all sample sizes in the sample_size vector

  # An alternative (shorter) way is to specify n = seq(5, 100, by = 5) directly 
  # within the pwr.t.test call:

  power_data <- pwr.t.test(
    d = 0.5, 
    n = seq(5, 100, by = 5), 
    sig.level = 0.05, 
    type = "two.sample", 
    alternative = "two.sided"
  )
  ```
</details>

7. Now call the `plot()` function with `x = power_data$n` and `y = power_data$power` as arguments together with the third argument `type = "b"` to generate a power curve for the sample sizes.

<details>
  <summary>Solution</summary>
  
  
  ```R
  plot(
    x = power_data$n, 
    y = power_data$power, 
    type = "b"
  )

  grid()  # this function is called to add grid lines to the plot
  ```
</details>

# ANOVA

Here we continue with the same dataset as in the previous part. Above we compared the two types of supplements. Here we will instead compare the length of odontoblasts between the three different doses (0.5, 1.0 and 2.0 mg/d). Which dose has the highest effect on the growth of odontoblasts?

1. Make a boxplot of the variable `len` in the dataframe `my_data`, use the variable `dose` as grouping variable.

<details>
  <summary>Solution</summary>
  
  
  ```R
  boxplot(my_data$len ~ my_data$dose)

  # there seems to be a dose response as the median increases with the dose.
  # 2.0 mg/d has the highest effect on the growth
  ```
</details>

## One-way ANOVA

2.  We are going to make a one-way anova with `dose` as independent variable. Since `dose` is a numerical variable we need to specify to the ANOVA function that it should be a factor variable. This can be done within the formula: `len ~ factor(dose)`.

    Use the `aov()` function to do the ANOVA and pipe it to the `summary()` function to look at the results. **_Is there a significant difference between the tooth lengths based on the dose?_**

<details>
  <summary>Solution</summary>
  
  
  ```R
  aov(len ~ factor(dose), data = my_data) %>%
  summary
  ```
</details>

## Two-way ANOVA

3. Now add the `supp` variable to the model in the ANOVA to make it a 2-way ANOVA. **_What do you observe now?_**

<details>
  <summary>Solution</summary>
  
  
  ```R
  aov(len ~ factor(dose) + supp, data = my_data) %>%
  summary
  ```
</details>

## Post ANOVA

In the one-way ANOVA above we saw that there is a difference in odontoblast length based on the dose. From the output of one-way ANOVA we can't determine where the possible difference occurs. To identify between which doses there is a significant difference we need to perform a post-hoc test.
  
4. Make post-hoc test using the Tukey's honest signficant difference test `TukeyHSD()`. Between which doses can we see a significant difference?

<details>
  <summary>Solution</summary>
  
  
  ```R
  aov(len ~ factor(dose), data = my_data) %>%
  TukeyHSD
  
  # all pairwise comparisons are significant since p < 0.05 for all of them
  ```
</details>

# Contingency tables

Here we will use the datset `survey` which is found in the package `MASS`, this dataset contains the responses of 237 Statistics I students at the University of Adelaide to a number of questions.

We will examine if there is any association between frequency of exercise (the column `Exer`) and gender (the column `Sex`). 

1. Load the `survey` data from the `MASS` package and store in an object called `survey` by running the following code

  ```R
  survey <- MASS::survey
  ```

2. To get an overview of the data, create a cross table with the two variables, survey$Exer and survey$Sex, using the `table()` function. You should get a table similar to the one below (with the number of observations filled in):

|   |Female|Male|
|---|---|---|
|Freq| | |
|None| | |
|Some| | |


<details>
  <summary>Solution</summary>
  
  
  ```R
  table(survey$Exer, survey$Sex)
  ```
</details>
 
3. Test if there is an association between exercise and gender using a [Chi-square test](https://en.wikipedia.org/wiki/Chi-squared_test) by piping the table from above to `chisq.test`. The test rejects the null hypothesis of independence when the p-value is less than or equal to the significance level of 0.05.

<details>
  <summary>Solution</summary>
  
  
  ```R
  table(survey$Exer, survey$Sex) %>%
  chisq.test
  ```
</details>

4. Historically, a Chi-square test has been preferred since it is easier to compute than the [Fisher exact test](https://en.wikipedia.org/wiki/Fisher%27s_exact_test) since the Chi-square test relies on approximations. Today when computing power is no longer an issue the Fisher exact test is preferred especially for small sample sizes. Make a Fisher exact test with the same variables by piping the same table to `fisher.test`. Compare the p-value with the result from the Chi-square test.

<details>
  <summary>Solution</summary>
  
  
  ```R
  table(survey$Exer, survey$Sex) %>%
  fisher.test

  # no big difference as the sample size is not that small
  ```
</details>

# Linear regression and logistic regression

Linear and logistic regression are used to describe data and to explain the relationship between the indpendent and dependent variables. In linear regression, the dependent variable is continuous (i.e. height and weight) while in logistic regression the dependent variable is binary (i.e. yes/no, case/control, male/female).

## Linear regression

Here we will use the built-in dataset mtcars. The dataset consists of data from 1974 Motor Trend US magazine, and comprises fuel consumption and 10 aspects of automobile design and performance for 32 automobiles (1973–74 models).

1. Get to know the dataset `mtcars` by typing `?mtcars` and `head(mtcars)`

4. Make a scatterplot to visualize the relation car weight (1000 lbs) and plasma fuel consumption (Miles/(US) gallon) using the variables `wt` and `mpg`. How does the increase in weight influence the fuel consumption?

<details>
  <summary>Solution</summary>
  
  
  ```R
  plot(mtcars$wt, mtcars$mpg)

  # An an increase in weight leads to less miles per gallon.
  ```
</details>

5. Now make a linear regression using the function `lm()` with `mpg` as response and `wt` as predictor and pipe it to the `summary` function to get coefficient estimates and p-values. **_What is the slope? Is there a significant effect of weight on fuel consumption?_**

<details>
  <summary>Solution</summary>
  
  
  ```R
  lm(mpg ~ wt, data = mtcars) %>%
  summary

  # The slope is -5.3445 which suggests that, on average, a one-unit increase in wt is associated with a 5.3445-unit decrease in mpg, assuming a linear relationship
  # Yes, there is a significant effect of weight on fuel consumption since p = 1.29e-10 is less than the significance level of 0.05
  ```
</details>

## Logistic Regression

In the previous part we had a continuous response variable. Here we will instead consider the binary outcome variable `diabetes`.

For binary outcome we can use logistic regression which can be performed using the `glm()` function in R. The abbrevation *glm* stands for *generalized linear models* and as the name indicates, this function can do many different regressions. You need to specify the `family` argument to tell R which type of data you have. The default is `family="gaussian"` which is the same as a linear regression using `lm()`. To make a logistic regression you need to set `family="binomial"`. Have a look at the help file for `glm()` to see other options for `family`.


1. Now using the built-in dataset mtcars we will perform a logistic regression to evaluate if the fuel consumption (`mpg`) is associated with if a car has automatic or manual transmission. To do this we use the `glm` command with the argument `family="binomial` to perform the logistic regression. Start by piping mtcars to the `glm` function, using `am` as the dependent variable and `mpg` as the independent variable, and pipe it further to the `summary` function to get coefficient estimates and p-values. HINT: `data = .` within the `glm` command tells it to use the data on the left side of the pipe.



<details>
  <summary>Solution</summary>
  
  
  ```R
  mtcars %>%
  glm(am ~ mpg,
  data = .,
  family = "binomial") %>%  
  summary()
  ```
</details>

3. **_Does `mpg` have a significant association with whether a car is automatic?_**

<details>
  <summary>Solution</summary>
  
  
  ```R
  Yes, there is a significant association between mpg and and the dependent variable am (whether a car is automatic), since 
  p = 0.00751 is less than the significance level of 0.05
  ```
</details>

4. Now make a new logistic regression where you also include the variable weight (`wt`) to the previous model. **Is mpg still significant and what is the p-value?**

<details>
  <summary>Solution</summary>
  
  
  ```R
  mtcars %>%
  glm(am ~ mpg + wt,
  data = .,
  family = "binomial") %>%  
  summary()

  # mpg is not significant anymore since p > 0.05 (=0.176)
  ```
</details>

The output from the `glm()` function gives effect estimates of log odds. Often it's preferred to present odds ratio. Log odds can be translated to odds ratios by taking the exponent of the log odds. To do this using base R is tedious therefore we will use the clever help function `tidy` from the `broom` package.  To translate the output from the `glm()` function into odds ratios and confidence intervals we set the arguments `exponentiate=TRUE` and `conf.int=TRUE` within the `tidy` function. HINT: If you want extra information about how to use tidy in conjunction with glm use `?tidy.glm`

5. Install and load the `broom` package.

  ```R
  install.packages("broom")
  ```

6. Now, extract odds ratios and confidence intervals for the model in exercise 4.

<details>
  <summary>Solution</summary>
  
  
  ```R
  mtcars %>%
  glm(am ~ mpg + wt,
  data = .,
  family = "binomial") %>%  
  tidy(exponentiate = TRUE, conf.int = TRUE)
  ```
</details>

One can use a likelihood ratio test to test if a relatively more complex model is significantly better than a simpler model. This is done by specifying the two fitted models as two separate arguments to the `anova()` function together with the third argument `test="LRT"`, i.e. `anova(mod0, mod1, test="LRT")`.

7. Use the `anova()` function to test if the more complex model from exercise 4 is significantly better than the simpler model from exercise 1. HINT: Start by storing the models in objects called `mod0` and `mod1` and then call the `anova()` function with them as two separate arguments together with the third argument `test="LRT"`.

<details>
  <summary>Solution</summary>
  
  
  ```R
  mod0 <- mtcars %>%
    glm(am ~ mpg,
    data = .,
    family = "binomial")


  mod1 <- mtcars %>%
    glm(am ~ mpg + wt,
    data = .,
    family = "binomial")


  anova(mod0, mod1, test = "LRT")

  #  p=0.0004089, so inclusion of the variable wt provides a significantly better fit 
  #  to the data compared to the simpler model.
  ```
</details>

# If you have time: Survival Analysis

In survival analysis we want to find variables associated with the time to some event (i.e. death, disease ocurrence etc.). We can use Kaplan-Meier curves to compare survival curves between groups and we can use Proportional Hazards Cox Regression to test for association with survival time. 

Here we will be using the R package `survival`, and the dataset `ovarian` that comes with the package. The dataset contains survival data from a randomized trial comparing two treatments for ovarian cancer. We are going to study if the two treatments have different effect on survival.

1. Install and load the `survival` package.

  ```R
  install.packages("survival")
  library(survival)
  ```

2. Get to know the dataset by typing `?ovarian` and use the function `head` on the dataset `ovarian`.

4. Find out how many patients are included in the data.

<details>
  <summary>Solution</summary>
  
  
  ```R
  nrow(ovarian)
  ```
</details>

5. The variables `resid.ds`, `rx` and `ecog.ps` are binary variables, but they are stored as numeric (1s and 2s). For the values to make sense we will recode them to factors. `rx` contains information of which treatment the patient had. Create a new variable, `rx_cat`, within the dataset by changing them to factors with

    ``` r
    ovarian$rx_cat <- factor(ovarian$rx, 
                             levels = c("1", "2"), 
                             labels = c("A", "B"))
    ```

Similarly recode resid.ds to `resid_cat` and ecog.ps to `ecog_cat` according to the specification below:

  - `resid.ds` (regression of tumors) 1 -> no and
    2 -> yes
  - `ecog.ps` (patients´ performance) 1 -> good and
    2 -> bad

<details>
  <summary>Solution</summary>
  
  
  ```R
  ovarian$resid_cat <- factor(ovarian$resid.ds, 
                         levels = c("1", "2"), 
                         labels = c("no", "yes"))

  ovarian$ecog_cat <- factor(ovarian$ecog.ps, 
                         levels = c("1", "2"), 
                         labels = c("good", "bad"))
  ```
</details>

## Survival response

The variable `futime` contains the follow-up time for the patients and `fustat` contains information about event and censoring status (1 = event, 0 = censored). In survival analysis the survival time together with the event status is the response variable. To do the analyses we need to create a survival object that we can use as a response variable in our analyses. This is done by using the Surv function with time and event variable as arguments.

7. Use the `Surv()` function with follow-up time (`futime`) and event (`fustat`) as arguments. Store this object in a variable named `surv`.

<details>
  <summary>Solution</summary>
  
  
  ```R
  surv <- Surv(ovarian$futime, ovarian$fustat)
  ```
</details>

8. **_How many patients are censored?_**. HINT: Use the `table` function on the variable `fustat` and look for the number of zeroes.

<details>
  <summary>Solution</summary>
  
  
  ```R
  table(ovarian$fustat)

  # 14 patients are censored
  ```
</details>

## Kaplan Meier Plots

A Kaplan Meier plot is good visualization of the survival in the two treatment groups. The Kaplan Meier plot function needs a fitted model to create the plot and we will use a function called `survfit()` to fit the model. Check the help on the `survfit()` function for details.

9. Fit a model with your `surv` object as response and group variable `rx_cat` from the `ovarian` data as predictor

<details>
  <summary>Solution</summary>
  
  
  ```R
  survfit(surv ~ rx_cat, data=ovarian)
  # Note that a shorter way is to specify the Surv object within the survfit call:
  # survfit(Surv(futime, fustat) ~ rx_cat, data=ovarian)
  ```
</details>

10. Create a simple Kaplan Meier plot by piping the previous code from exercise 9 to the `plot` function
<details>
  <summary>Solution</summary>
  
  
  ```R
  survfit(surv ~ rx_cat, data=ovarian) %>%
  plot
  ```
</details>


11. The plot in exercise 10 is not visually appealing and not informative. To make better Kaplan Meier plots, install and load the `survminer` package. This package contains the function `ggsurvplot` to plot Kaplan Meier curve.

  ```R
  install.packages("survminer")
  library(survminer)
  ```

12. Create a Kaplan Meier plot by piping the code from exercise 9 to the `ggsurvplot` function. To display a p-value within the plot, add the argument `pval = TRUE` within the `ggsurvplot` function. **_Which treatment is better and is the difference statistically significant?_**

<details>
  <summary>Solution</summary>
  
  
  ```R
  survfit(surv ~ rx_cat, data=ovarian) %>%
  ggsurvplot(pval=TRUE)

  # Treatment B is better but not significantly better since p > 0.05 (p = 0.3)
  ```
</details>

## Cox regression

Kaplan Meier is a non-parametric method that is limited to study one factor variable at the time. An alternative to this is the Cox proportional hazards regression which is a semi-parametric method for which both factors and continuous can be included in the model. This makes it possible to adjust for certain (confounding) variables.

13. Use the `coxph()` function to make a Cox regression. Let your `surv` object be response and add the predictors `rx_cat` and `ecog_cat` and pipe it to the function `tidy` from the `broom` package similar to what we did in exercise 6 under Logistic regression. Set the arguments `exponentiate = TRUE` and `conf.int = TRUE` so that it outputs hazard ratios and matching confidence intervals. **What is the p-value for rx_cat after adjusting for ecog_cat?**

<details>
  <summary>Solution</summary>
  
  
  ```R
  coxph(surv ~ rx_cat + ecog_cat, data=ovarian) %>%
  tidy(exponentiate=TRUE, conf.int=TRUE)

  # The rx_cat variable is not statistically significant at the significance level 0.05 (p = 0.325)

  ```
</details>

14. One way to display the results from a Cox proportional hazards regression is to visualize them using forestplot. This could be done by using the function `ggforest` from the package `survminer`. Replace the code after the last pipe in the previous exercise with `ggforest(data=ovarian)`

<details>
  <summary>Solution</summary>
  
  
  ```R
  coxph(surv ~ rx_cat + ecog_cat, data=ovarian) %>%
  ggforest(data=ovarian)

  ```
</details>


***
### Home: [R programming](https://github.com/BDC-training/VT26/blob/main/README.md) 
***

_Developed by Malin Östensson, 2017. Modified by Maria Nethander, 2018_. Modified by Björn Andersson and Jari Martikainen, 2024.