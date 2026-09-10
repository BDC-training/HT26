_Course: VT26 R programming (SC00035)_
<hr>

In this exercise we will standardize a number of variables. We will do it in two different ways: 

* The first solution will be to loop through the variables and standardize them one by one. 
* The second solution will be to write our own function for standardizing a variable, and then use that function to standardize all variables simultaneously. 

In total we will therefore need to write three different scripts. Try to keep your scripts clean from unnecessary code and remember to include comments of what you do so that you later on will be able to recall what the code does.

<hr>

# Script 1

## Setup

1. Create or select a folder/directory on your computer where you will do all work in this exercise. We will call this folder your _working directory_.

2. Download the file *[exampledata_forRScripWritingExercise.txt](https://raw.githubusercontent.com/bcfgothenburg/VT19/master/exampledata_forRScripWritingExercise.txt)* (can also be found *[here](https://github.com/bcfgothenburg/VT19/blob/master/exampledata_forRScripWritingExercise.txt)*) and save it in your working directory.

3. Open a new R script in RStudio and save it into your working directory. If you prefer to work with R Notebooks, this part of the exercise can be done equally well in a notebook.

4. The first thing you should do with this script is to set the R working directory to be the folder you selected. Use the function `setwd()` to set your working directory.

## Read data

5. Use the `read.table()` function to read in the example data. Store it in a variable called `data`.
    <details>
    <summary>Solution</summary>
  
  
    ```R
    data <- read.table("exampledata_forRScripWritingExercise.txt", header=T, sep="\t", as.is=T)
    ```
    </details>


6. In the **console** you can use the functions `summary()`, `head()`, `dim()` etc. to explore the data. You can also use `hist()` on separate columns from the data to see how the variables are distributed. Remember that this is just for you to get to know the data. It is not necessary for you to be able to replicate the analysis. Therefore you don’t need save this in the script. (You could of course write a QC script where you do all of this and print the results to files, but we don’t need to worry about that now)

## Analysis

For all variables in the data we will calculate the standardized values. That means that we will calculate the difference between the original value for `variable_N` and the mean of `variable_N` and then divide the difference with the standard deviation of `variable_N`. That is, for individual _i_ the standardized value is: 

<p align="center">
Z<sub>n,i</sub> = (variable<sub>n,i</sub>-mean(variable<sub>n</sub>)) / (sd(variable<sub>n</sub>))
</p>

We will use the `mean()` function to calculate means, the `sd()` function to calculate standard deviation and we will use a for-loop to loop through all variables. The standardized values will be saved in a new data frame.

7. Save the ID column from the example data in a variable that you call `zdata`. This will be the fist column in our new standardized data.
    <details>
    <summary>Solution</summary>
  
  
    ```R
    zdata <- data$ID
    ```
    </details>

8. We will now create the frame of the for-loop. We will use loop variable `i` that will tell us which column of the data we are working with. Since the first column of the data contains sample ID we should start our loop in the second column and continue to the number of columns we have. 

    The code begins with: 

    ```r
    for(i in
    ```

You can use the function `ncol()` to find out the number of columns in your data, unless you haven’t done it already before. Remember to frame all code within the loop with curly brackets (  ``{   }``  ).
  <details>
  <summary>Solution</summary>
  
  
  ```R
  for (i in 2:ncol(data)) {
    # Here is where the content of the loop will be
  }
  ```
  </details> 

9. When the frame of the loop is set we can start to fill it with code. 
    1. Since `i` is our loop variable we will work with the vector variable `data[,i]` within the loop. 
    2. Use the `mean()` and `sd()` functions to calculate the mean and standard deviation of the current column.
    3. Calculate the standardized value as described above and save them into a variable called `z`. 

    You can do the steps above either by saving the mean and SD in variables which you use in the standardization calculation, or you use the functions directly in the formula. You select the method you prefer.
    <details>
    <summary>Solution</summary>
  
    ```R
    for (i in 2:ncol(data)) {
      m <- mean(data[,i])
      s <- sd(data[,i])
      z <- (data[,i]-m)/s
    }
    ```
    </details>  

10. Since our variable `z` will be overwritten in the next round of the loop we need to save it into our new data. 
    
    Within the loop, use the function `cbind()` to bind `zdata` (as first argument) with `z` (as second argument) and save it again as `zdata`. 

    When you do this the variable `zdata` will go from being just a vector with IDs to becoming a matrix with IDs in the first column and standardized values in the following columns.
    <details>
    <summary>Solution</summary>
  
    ```R
    for (i in 2:ncol(data)) {
      m <- mean(data[,i])
      s <- sd(data[,i])
      z <- (data[,i]-m)/s
      zdata <- cbind(zdata, z)
    }
    ```
    </details> 
	
11. The loop is now complete and you can run it to see if it works. It is possible that you will get some error message when you run the loop. If you do get an error, read the message and try to understand where you did a mistake.
	
## Save results

When you have successfully run the loop you will have a matrix called `zdata` which is the same size as your original data and that should contain standardized values of the variables in the same order as the original data. 

To be able to save the new data for future use, we need to give it column names that are informative. It is also often more convenient to work with data frames rather than matrices so we will translate this matrix into a data frame. 

12. Use the function `data.frame()` to translate `zdata` to a data frame. You can save the data frame with the same name (`zdata`) as the matrix since you won’t need the matrix anymore.
    <details>
    <summary>Solution</summary>
  
    ```R
    zdata <- data.frame(zdata)
    ```
    </details> 

13. To give the new data frame names that corresponds to the original data we can use a function called `paste()`. This function takes vectors, translates them into characters and concatenates them using a separator that you can give as an argument. Depending on if the length of the vectors are 1 or higher than 1, the result will differ a bit. This is a useful function that we recommend you to explore but for now you get the code to rename your `zdata`:

    ```r
    names(zdata) <- c("ID", paste("z_", names(data)[2:ncol(data)], sep = ""))
    ```

14. Use the function `write.table()` to print your standardized data to a file. The file should have column names, but not row names. Quotes around characters are not recommended. If you have Swedish setting on your laptop you can select to set the decimal character to be ``,``, otherwise you can leave it as default. The default column separator is space. If you want to have comma or tab you need to specify that.
    <details>
    <summary>Solution</summary>
  
    ```R
    write.table(zdata,"stdOfExampleData_20240224.txt", sep="\t", row.names=F, col.names=T, quote=F)
    ```
    </details> 

15. If you haven’t already done it, save your script for future use. Also check if you have enough comments that will help you understand the code later.

16. At last, open your new data file in a spread sheet program (i.e. Excel) and check what you got. **_Does it look OK?_**
    <details>
    <summary>Solution Complete Script</summary>
  
    ```R
    #setwd("...") #Set the working directory to your own folder

    data<-read.table("exampledata_forRScripWritingExercise.txt", header=T, sep="\t", as.is=T)

    zdata<-data$ID

    for(i in 2:ncol(data)){
       m<-mean(data[,i])
       s<-sd(data[,i])
       z<-(data[,i]-m)/s
       zdata<-cbind(zdata,z)
    }


    zdata<-data.frame(zdata)
    names(zdata)<-c("ID", paste("z_", names(data)[2:ncol(data)],sep=""))

    write.table(zdata,"stdOfExampleData_20240224.txt", sep="\t", row.names=F, col.names=T, quote=F)
    ```
    </details> 

# Script 2

We will use the same working directory and data as in the previous task. The mission is also the same, but we will now do it in a different way. 

Why? Using loops in R is often not recommended. In R, looping is quite inefficient and slow. Instead R can work simultaneously on multiple vectors at the same time which makes much faster calculations. 

Our second script will therefore be a function that calculates standardized values of a vector and our third script will use that function to standardize the entire data at once.

## Write your own function

1. Open a new R script file and save it as ``myzscore.R``. Since you will call this script from another script in the next step we recommend you to use an R *Script* and not an R *Notebook* for this part of the exercise.

2. Use the function `function()` to create your home made function `myzscore()`. All code that the function will run, should be written within curly brackets. Your function should take one vector called `x` as argument, and calculate the standardized values from it. You can copy your code from the previous script and adjust it so it standardizes `x` instead of `data[,i]`. Then use the `return()` function to define what your function should send back to you.
    <details>
    <summary>Hint</summary>

    ```R
    myzscore <- function(x) {
      # Here is where we will calculate the value of z
      return(z)
    }
    ```
    </details>

    <details>
    <summary>Solution</summary>
    
    ```R
    
    myzscore <- function(x) {
      m <- mean(x)
      s <- sd(x)
      z <- (x-m)/s
      return(z)
    }
    ```
    </details>  

3. Save the script.

# Script 3

We will now create one script that uses our function `myzscore()` to calculate the standardized values of the variables in the example data.

1. Open a new R script or if you prefer, an R Notebook. Set the working directory to the same as in your first script. Read in the same data as in your first script.
    <details>
    <summary>Solution</summary>

    ```R
    data <- read.table("exampledata_forRScripWritingExercise.txt", header=T, sep="\t", as.is=T)
    ```
    </details>

2. To be able to use your home made function you need to load it in this script. This can be done using the function `source()` with your function script as input. The `source()` function takes a file as argument and will run the code and parse the information from the file to the current R session. Use `source`to load your own function.
    <details>
    <summary>Solution</summary>

    ```R
    source("myzscore.R")
    ```
    </details>

Since we now have a function that can calculate the standardized values as we want them, we can use a different function called `apply()` to apply our function to one of the margins of the data frame where we have our data. 

The `apply()` function takes for example a data frame or matrix as first argument. The second argument is called MARGIN and defines which direction the calculations should be performed. If you set MARGIN=1 for a matrix/data frame, `apply()` will do the calculations per row, and if you set MARGIN=2 the calculations are done per column. The third argument to `apply()` is the name of the function to use. 

3. Use `apply()` to calculate standardize values of the variables in the original data. Note that 
    1. You should **only** give the columns that contains variables, not the ID column, as input. 
    2. Calculations should be performed by column, since we have variables in columns and samples in rows. 
    3. Give your own function `myzscore()` as the function input. 

    The output from `apply()` can be stored in a new matrix called `zdata`.
    <details>
    <summary>Hint</summary>

    ```R
    # The data we want to use can be accessed through:
    data[,2:ncol(data)]
    # The input margin to apply should be set to 2, since we want to do our operations on the columns 
    ```
    </details>

    <details>
    <summary>Solution</summary>

    ```R
    zdata <- apply(data[,2:ncol(data)],2,myzscore)
    ```
    </details>

4. The new `zdata` will only contain variable columns but lack the ID column. Pick the ID column from the original data and use `cbind()` to add it as the **first** column in `zdata`.
    <details>
    <summary>Solution</summary>

    ```R
    zdata <- cbind(data$ID, zdata)
    ```
    </details>

5. You can use the same code as in the first script to translate the matrix `zdata` into a data frame and give it informative column names.
    <details>
    <summary>Solution</summary>

    ```R
    zdata <- data.frame(zdata)
    names(zdata) <- c("ID", paste("z_", names(data)[2:ncol(data)],sep=""))
    ```
    </details>

6. Write this `zdata` into a new file using `write.table()`. Use a different file name from what you did in the first script, the rest of the settings can be the same.
    <details>
    <summary>Solution</summary>

    ```R
    write.table(zdata,"usingApplyData_20240224.txt", sep="\t", row.names=F, col.names=T, quote=F)
    ```
    </details>

7. Remember to complement your code with useful comments and save your script.

    <details>
    <summary>Solution Complete Script</summary>

    ```R
    #setwd("...") #Set the working directory to your own folder

    data<-read.table("exampledata_forRScripWritingExercise.txt", header=T, sep="\t", as.is=T)

    source("myzscore.R")

    zdata<-apply(data[,2:ncol(data)],2,myzscore)

    zdata<-cbind(data$ID, zdata)
    zdata<-data.frame(zdata)
    names(zdata)<-c("ID", paste("z_", names(data)[2:ncol(data)],sep=""))

    write.table(zdata,"usingApplyData_20240224.txt", sep="\t", row.names=F, col.names=T, quote=F)
    ```
    </details>

# If you have time

In this exercise you will write a script on you own with just the problem defined. You will work with data that you need to install from a github repository. This is data from school children. The data contains sex coded as ``m`` and ``f``, age in years and in months, height in Inches and weight in Lb.

To install the data package you need to run the following code in RStudio as **administrator**

``` r
install.packages("remotes")
remotes::install_github("wch/gcookbook")
```

To load the data you first need to load the package with this code

``` r
library("gcookbook")
```

The data is then stored under the variable `heightweight`.


### Your task:

Calculate the mean Body Mass Index (BMI) in kg/m^2, for males and females separately in the given set of school children.

1. Write pseudocode to solve the problem

2. Translate the pseudocode to R code

3. Run your code and solve possible errors
 
    <details>
    <summary>Solution</summary>

    ```R
    ### Pseudocode

    #Load the data

    #Translate height from inches to meters
    #Translate weight from lb to kg

    #Calculate BMI

    #Calculate mean BMI for sex=="f"
    #Calculate mean BMI for sex=="m"


    ### R code
    library("gcookbook")
    data(heightweigh)

    length_m<-(heightweight$heightIn*2.54)/100
    weight_kg<-heightweight$weightLb*0.453592
    bmi<-weight_kg/(length_m^2)

    mean(bmi[heightweight$sex=="f"])
    mean(bmi[heightweight$sex=="m"])
    ```
    </details>
***
### Home: [R programming](https://github.com/BDC-training/VT26/blob/main/README.md) 
***
_Developed by Maria Nethander, 2017_, _Modified by Fanny Berglund, 2024_