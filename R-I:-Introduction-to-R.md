_Course: VT26 R programming (SC00035)_
<hr>

# Getting Started

1. Open ``RStudio`` (or RGui)

1. Type a simple mathematical expression (e.g. 1+2) in the console and press enter. The code is run immediately. 

    You can see the code you ran in the console, but it cannot be edited. You can however scroll through the latest commands by pushing the “up” or “down” keys on your keyboard, edit them, and run them again.

# Working with scripts

1. Open a new R script (``File->New file->R script``) and save it in a suitable folder. It is a good idea to save it using an ``.R`` ending, so you know that it is R code in the file. Also, using spaces in file names may cause you trouble later on, so try to use “_” instead. 

    All code you want to save should be written into scripts like this or, as we will see later, into other types of R files. Then you can go back later on to see what you did. In a script you can use `#` to write comments to your code. Commenting your code will make it easier to read or to remember what the code does. Anything written on a line after `#` will be disregarded when running the code.

1. Type the same simple expression as before (e.g. 1+2) into your new file. You can run a line by placing the cursor on the line and then: 

    1. either click the ``Run`` button in the top left corner of the editor, 
    1. or press `Ctrl+Enter` (in Rstudio) 
    1. or press `Ctrl+R` (in RGui).

1. Assign the expression to a variable called `my_expr` and run the code again

    <details>
        <summary>Solution</summary>

      ```R
      my_expr <- 1+2
      ```
    </details>

1. On a new line use the `print()` function with the variable `my_expr`  as argument to print the value of your new variable in the console.

    The `print()`  function is used if you run your entire script from a different location or a from a different script. We will see more of that later.</p>
    <details>
        <summary>Solution</summary>

      ```R
      print(my_expr)
      ```
    </details>

1. **_What happens if you put citation marks around the name of the variable in the `print()` function?_**
    <details>
        <summary>Solution</summary>

      ```R
      print("my_expr")
      ```
    </details>

1. Save the script and then run the entire script. You can do this by:

    1. marking all code (using the pointer or `Ctrl+A`) and press `Ctrl+Enter`, `Ctrl+R` or the run button
    1. pressing the “Source” button in the top right of the editor, which runs the entire script
    1. Using the `source()` function in a different script, with the script you want to run as argument (including the path to where it is stored), e.g: `source("C:/My_R_Scripts/My_script.R")`. This is the case where the `print()` function will be needed (without it nothing will be printed in the console).

<!--
# Working with R Notebooks

1. In RStudio: Open an R notebook (``File -> New File -> R Notebook``).  (This cannot be done if you work in RGUI)

   In an R Notebook you can write comments and instructions to your code just as ordinary text. You should **_not_** use the ``#`` character in front since this character now is used to make something a header. For this course it is enough to write simple text, but if you want to use headers, bold/italc, lists, links etc. you need to use the R Markdown language. You can find some information on this page: [github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet)

   When you open a new R Notebook some text and code is already written. You can read it the first time as it contains information about how to use the notebook. When you have read it, you can delete it, since it is not relevant for your own notebook. 

1. Write a short introduction to the notebook i.e. name of the course, exercise, the date or something like that. 

1. Insert a new code chunk: Press ``Insert`` in the top right corner and select ``R``. R Notebooks can also manage other types of code, which is why you find more alternatives here.

1. In the code chunk you should now source the script you wrote in the last exercise. Within the chunk: write `source("C:/My_R_Scripts/My_script.R")` but of course with the link to your own script.

1. Run the code either using the play button in the top right corner of the chunk, or the run button in the top right corner of the editor.

1. **From now on**: Before you move to the next exercise we encourage you to write some notes related to your code in the current exercise. That is, write some text either above or below the code chunk, explaining what's in the chunk. This will help you to remember what the code does, if you like to go back later on.
-->

# Vectors

1. Create a vector with the numbers 1, 2, … , 10 and assign it to the variable `x`.
    <details>
        <summary>Solution</summary>

      ```R
      x <- 1:10
      ```
    </details> 

1. Select the five first elements in `x` and assign them to the variable `x1`.
    <details>
        <summary>Solution</summary>

      ```R
      x1 <- x[1:5]
      ```
    </details> 

1. Select the five last elements in x and assign them to the variable `x2`.
    <details>
        <summary>Solution</summary>

      ```R
      x2 <- x[6:10]
      ```
    </details>

1. Calculate the sum of `x1` and `x2`. **_What did you get?_**
    <details>
        <summary>Solution</summary>

      ```R
      x1 + x2
      ```
    </details>

1. Now repeat the three steps above but take the three first elements and the seven last elements. Calculate the sum of these two vectors. **_What did you get now?_**

    **Here is a warning!** You can take sums of vectors of different length and get a result. The shorter vector will be recycled until the end of the longer vector. If you’re lucky you get a warning but always check the results so you got what you expected.
    <details>
        <summary>Solution</summary>

      ```R
      x1<-x[1:3]
      x2<-x[4:10]
      x1+x2
      ```
    </details>

1. Now set the variable `n` to equal 10 and create the following vector

    ```r
    y1 <- 1:n-1
    ```

    **_What did you get and why?_**
    <details>
        <summary>Solution</summary>

      ```R
      n <- 10
      y1 <- 1:n-1
      ```
    </details>
    Create a second vector

    ```r
    y2 <- 1:(n-1)
    ```

    **_What is the difference?_**

    What we see here is an example of how operations are prioritized. `:` has higher priority than `-`. Higher prioritization will be executed first. This is good to be aware of. Try out another example:

    ```r
    1+2*3 
    ```

    **_What do you think the result is? Which operation has the highest priority? How can you write this so that the result equals 9?_**
    <details>
        <summary>Solution</summary>

      ```R
      (1+2)*3
      ```
    </details>

# Factors

1. Insert a new R code chunk where you create two vectors, `measure` and `type`, of length 10 using the following code

    ```r
    measure <- sample(1:3, 10, replace=T)
    type <- sample(c("a", "b", "c"), 10, replace=T)
    ```

1. Translate `type` into a factor using the function `as.factor()`.
    <details>
        <summary>Solution</summary>

      ```R
      type <- as.factor(type)
      ```
    </details>

1. Print out `type` to see how it looks.
    <details>
        <summary>Solution</summary>

      ```R
      print(type)
      ```
    </details>

1. Now print the expression `c(type,"d")`.
     <details>
        <summary>Solution</summary>

      ```R
      print(c(type,"d"))
      ```
    </details>

   You will notice that ``a``, ``b`` and ``c`` in `type` suddenly changed to numbers. Factors are troublesome in many ways and you need to be careful when you work with them, but when we can use them as categories in statistical analysis they are really useful. We will work more with that in the statistics part of the course but here is a short example. Copy and run the code:

    ```r
    tapply(measure, type, mean)
    ```

    This is a very quick and easy way to calculate the mean of `measure` within each factor in `type`. To place it in a real context, you can imagine that `type` was some sample type and `measure` was some measure performed on the samples.

# Data frames

1. We will continue with the vectors `type` and `measure` from the previous task. Create a data frame called `mydata` with these two vectors as columns. You can use the function `data.frame()`.
     <details>
        <summary>Solution</summary>

      ```R
      mydata <- data.frame(type, measure)
      ```
    </details> 

1. Use the functions 
    1. `head()`,
    2. `tail()`, 
    3. `summary()`, 
    4. and `dim()`

    to look at the data frame. 

1. Now, let’s say that we are only interested in sample type `a`. Look at the column called `type` using the dollar sign `$`.
     <details>
        <summary>Solution</summary>

      ```R
      mydata$type
      ```
    </details> 

1. Use the `==` operator to find out which of the elements in this column that have the value `a`. Type 

    ```r
    mydata$type == "a"
    ```

    **_What you got is a vector of _logical_ values?_** 

1. Use the `which()` function with the expression above as argument. This will give you a vector with the positions of the type vector that equals `a`. Assign the vector of positions to a variable that you call `pos`
     <details>
        <summary>Solution</summary>

      ```R
      pos <- which(mydata$type=="a")
      ```
    </details> 

1. Use square brackets to extract a subset of the data frame that contains type `a` (i.e the row numbers in `pos`). Inside the square brackets you first tell which rows, then which columns you want to see. Remember from the lecture that an empty space means that all should be used.
     <details>
        <summary>Solution</summary>

      ```R
      mydata[pos,]
      ```
    </details> 

1. Assign the subset to a variable called `mydata_out`.
     <details>
        <summary>Solution</summary>

      ```R
      mydata_out <- mydata[pos,]
      ```
    </details> 

# Reading and writing text files

1. We will now print `mydata_out`  to a tab separated text file using the `write.table()` function. Play around with the arguments
    1. `col.names`
    2. `row.names`
    3. `quote`
    4. `sep`

    in `write.table()` and see what happens. You can open the file in a simple text editor to see how the output changes when you change the arguments.
     <details>
        <summary>Solution</summary>

      ```R
      write.table(mydata_out, 
            "labtest20240422.txt",
            col.names=T, row.names=F, quote=F, sep="\t")
      ```
    </details> 

1. Now use the `read.table()` function to read the same data into R again, and save it as `mydata_in`.
     <details>
        <summary>Solution</summary>

      ```R
      mydata_in <- read.table("labtest20240422.txt", 
            header = T, sep="\t", as.is = T)
      ```
    </details> 

1. **_Are `mydata_in` and `mydata_out` identical? Use the `summary()` function on each of them. Is there any difference?_**


***
### Home: [R programming](https://github.com/BDC-training/VT26/blob/main/README.md) 
***

_Developed by Maria Nethander, 2017_, 
_Modified by Fanny Berglund, 2024_