# Counting-Missing-Values-NA-in-R

To check for missing values in R you might be tempted to use the equality operator == with your vector on one side and NA on the other. Don’t!

If you insist, you’ll get a useless results.

x <- c(1, 5, NA, 3, NA)
x == NA
## [1] NA NA NA NA NA

Instead use the is.na() function.

is.na(x)
## [1] FALSE FALSE  TRUE FALSE  TRUE

Armed with that knowledge let’s explore how to calculate some basic summary statistics about missing values in your data. First of all, to count the total number of NAs in a vector you can simply sum() up the result of is.na().

sum(is.na(x))
## [1] 2

Confused why you can sum TRUE and FALSE values? R automatically converts logical vectors to integer vectors when using arithmetic functions. In the process TRUE gets turned to 1 and FALSE gets converted to 0. Thus, sum(is.na(x)) gives you the total number of missing values in x.

To get the proportion of missing values you can proceed by dividing the result of the previous operation by the length of the input vector.

sum(is.na(x)) / length(x)
## [1] 0.4

Look careful at the code above. Does that “formula” look somehow familiar? Summing up all elements in a vector and dividing by the total numbers of elements, that’s calculating the arithmetic mean! So, instead of using sum() and length() we can simply use mean() to get the proportion of NAs in a vector.

mean(is.na(x))
## [1] 0.4

Enough of vectors, though, let’s look at counting missing values in a data frame. To illustrate the concepts let me first add some missing values to the mtcars dataset.

data(mtcars)
set.seed(1)
mtcars[sample(1:nrow(mtcars), 5), sample(1:ncol(mtcars), 3)] <- NA

The is.na() function is generic and has a method for data frames so you can directly pass it a data frame as input.

na <- is.na(mtcars)
na[1:15, 1:7]
##                      mpg   cyl  disp    hp  drat    wt  qsec
## Mazda RX4          FALSE FALSE FALSE  TRUE FALSE FALSE  TRUE
## Mazda RX4 Wag      FALSE FALSE FALSE  TRUE FALSE FALSE  TRUE
## Datsun 710         FALSE FALSE FALSE FALSE FALSE FALSE FALSE
## Hornet 4 Drive     FALSE FALSE FALSE FALSE FALSE FALSE FALSE
## Hornet Sportabout  FALSE FALSE FALSE FALSE FALSE FALSE FALSE
## Valiant            FALSE FALSE FALSE FALSE FALSE FALSE FALSE
## Duster 360         FALSE FALSE FALSE FALSE FALSE FALSE FALSE
## Merc 240D          FALSE FALSE FALSE FALSE FALSE FALSE FALSE
## Merc 230           FALSE FALSE FALSE FALSE FALSE FALSE FALSE
## Merc 280           FALSE FALSE FALSE FALSE FALSE FALSE FALSE
## Merc 280C          FALSE FALSE FALSE  TRUE FALSE FALSE  TRUE
## Merc 450SE         FALSE FALSE FALSE FALSE FALSE FALSE FALSE
## Merc 450SL         FALSE FALSE FALSE FALSE FALSE FALSE FALSE
## Merc 450SLC        FALSE FALSE FALSE FALSE FALSE FALSE FALSE
## Cadillac Fleetwood FALSE FALSE FALSE FALSE FALSE FALSE FALSE

As you can see the result is a matrix of logical values. Getting the total number of NAs then is simple because sum() works with matrices as well as vectors.

sum(is.na(mtcars))
## [1] 15

Arguably, though, the total number of missing values in a dataset is a rather crude measure. It gets much more interesting if we look at missing values across variables and records in the dataset. That enables detecting patterns that might inform future modeling decisions.

Counting NAs across either rows or columns can be achieved by using the apply() function. This function takes three arguments: X is the input matrix, MARGIN is an integer, and FUN is the function to apply to each row or column. MARGIN = 1 means to apply the function across rows and MARGIN = 2 across columns.

apply(X = is.na(mtcars), MARGIN = 1, FUN = sum)
##           Mazda RX4       Mazda RX4 Wag          Datsun 710      Hornet 4 Drive 
##                   3                   3                   0                   0 
##   Hornet Sportabout             Valiant          Duster 360           Merc 240D 
##                   0                   0                   0                   0 
##            Merc 230            Merc 280           Merc 280C          Merc 450SE 
##                   0                   0                   3                   0 
##          Merc 450SL         Merc 450SLC  Cadillac Fleetwood Lincoln Continental 
##                   0                   0                   0                   0 
##   Chrysler Imperial            Fiat 128         Honda Civic      Toyota Corolla 
##                   0                   0                   0                   0 
##       Toyota Corona    Dodge Challenger         AMC Javelin          Camaro Z28 
##                   0                   0                   3                   0 
##    Pontiac Firebird           Fiat X1-9       Porsche 914-2        Lotus Europa 
##                   0                   0                   0                   0 
##      Ford Pantera L        Ferrari Dino       Maserati Bora          Volvo 142E 
##                   3                   0                   0                   0
apply(X = is.na(mtcars), MARGIN = 2, FUN = sum)
##  mpg  cyl disp   hp drat   wt qsec   vs   am gear carb 
##    0    0    0    5    0    0    5    0    5    0    0

If you want to get the proportion of missing values per row or column just change the FUN argument to mean.

apply(X = is.na(mtcars), MARGIN = 1, FUN = mean)
##           Mazda RX4       Mazda RX4 Wag          Datsun 710      Hornet 4 Drive 
##           0.2727273           0.2727273           0.0000000           0.0000000 
##   Hornet Sportabout             Valiant          Duster 360           Merc 240D 
##           0.0000000           0.0000000           0.0000000           0.0000000 
##            Merc 230            Merc 280           Merc 280C          Merc 450SE 
##           0.0000000           0.0000000           0.2727273           0.0000000 
##          Merc 450SL         Merc 450SLC  Cadillac Fleetwood Lincoln Continental 
##           0.0000000           0.0000000           0.0000000           0.0000000 
##   Chrysler Imperial            Fiat 128         Honda Civic      Toyota Corolla 
##           0.0000000           0.0000000           0.0000000           0.0000000 
##       Toyota Corona    Dodge Challenger         AMC Javelin          Camaro Z28 
##           0.0000000           0.0000000           0.2727273           0.0000000 
##    Pontiac Firebird           Fiat X1-9       Porsche 914-2        Lotus Europa 
##           0.0000000           0.0000000           0.0000000           0.0000000 
##      Ford Pantera L        Ferrari Dino       Maserati Bora          Volvo 142E 
##           0.2727273           0.0000000           0.0000000           0.0000000

To finish up this post, let’s have a quick look at how to visualize missing data. There are a lot of different packages for that purpose out there but I really like the {heatmaply} package which generates an interactive heatmap.

heatmaply::heatmaply_na(mtcars)

Neat, isn’t it?
