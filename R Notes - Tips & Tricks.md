# Tips and Tricks

## 1. Base R

> Using base R function that does the job and does it well and efficient, thats classy! (just like my golden retriever, Ollie)

![Ollie_classy](./IMAGES/ollie_classy.jpg)

### Great references

* [base R package **Official Documentation**](https://stat.ethz.ch/R-manual/R-devel/library/base/html/00Index.html)

* [**Vocabulary** from 'Advanced R'](http://adv-r.had.co.nz/Vocabulary.html)

### Base R functions that I'm not (ab)using enough

* **sample**

```R
> sample(10)
 [1] 10  7  8  5  3  1  2  9  4  6

> sample(1:2, size=10, replace=TRUE)> sample(1:2, size=10, replace=TRUE)
 [1] 1 1 2 2 2 1 1 2 2 1

# assign weight by passing in probability
> sample(1:2, size=10, replace=TRUE, prob=c(0.2,0.8)) 
 [1] 2 2 2 1 2 1 2 2 2 2

# split train and test dataset
ind <- sample(2, nrow(df), replace=TRUE, prob=c(0.3, 0.7))
train <- df[ind==1, ]
test <- df[ind==2, ]
```

* The good old **head**, **tail**, **subset**

```R
head(mtcars, 10) #tail
subset(iris, Sepal.Length == 5.1, select = c(Species))
```

* **dimnames**
Produces a length 2 list with all the row names and all the column names.

* **setequal**
```R
> setequal(c(1, 2), c(2, 1))
[1] TRUE
```

* **substr**
```R
substr("abcdef", 2, 4)
```

* **assign**, **get**
```R
> var <- "x"
> assign(var, 8)
> get("x")
[1] 8
```

* **pmax**
```R
> max(c(1, 2, 3), c(4, 5, 2))
[1] 5
> pmax(c(1, 2, 3), c(4, 5, 2)) # compares vectors element-wise
[1] 4 5 3
```

* **seq**, **seq_along**, **seq_len**, **seq_int**

```R
# Create row index column
df$ID <- seq.int(nrow(df))
```

* **&**, **&&**
```R
> a <- c(T,T,F)
> b <- c(F,T,T)
> a&b # pairwise element comparison with output of length a
[1] FALSE  TRUE FALSE
> a&&b # compare vectors, return length of 1
[1] FALSE
```

* **expand_grid**
```R
> head(expand.grid(1:3, 2:5)) # returns a data.frame!
  Var1 Var2
1    1    2
2    2    2
3    3    2
4    1    3
5    2    3
6    3    3
```

* **interaction**
```R
> fct1 <- factor(c("X", "Y", "Z"), levels = c("X", "Y", "Z"))
> fct2 <- factor(c(1, 2, 3), levels = c(1,2))
> interaction(fct1, fct2) # Merges together pairwise combinations of factors
[1] X.1  Y.2  <NA>
Levels: X.1 Y.1 Z.1 X.2 Y.2 Z.2
```

* **unlist**

```R
unique(unlist(list)) #return unique values out of a unpacked list
```

* **browser**
>A call to browser can be included in the body of a function. When reached, this causes a pause in the execution of the current expression and allows access to the R interpreter.

* **on.exit**

```R
on.exit(dbDisconnect(conn))
```

### Others

* `all`, `any`

* `cut`

* `duplicated`, `union`, `intersect`, `setdiff`

* `rep`, `rep_len` ( rep_len - faster, simplified version of rep(c(1,2), 3) )

* `rev`

* `apply`, `lapply`, `sapply`, `mapply`, `vapply`, `tapply`

## 2. How-to's

* How to use a data.frame as lookup table 
  * Think of `dictionary` in Python
```R
> dict
               dog       color
1 Golden Retriever        Gold
2    Border Collie Black&White
3           Poodle       Brown
> dog
                  dog
1       Golden Doodle
2    Golden Retriever
3 Australian Shepherd
4       Border Collie

> dog$color <- dict$color[match(dog$dog, dict$dog)]
> dog
                  dog       color
1       Golden Doodle        <NA>
2    Golden Retriever        Gold
3 Australian Shepherd        <NA>
4       Border Collie Black&White
```


## 3. Working in `tidyverse`

* `stringr::word`

```R
word(sentence, -1)
```

### String manipulation

* `str_split(..., simplify = TRUE)`
```R
> ollie <- c("eats_a_lot",
+            "sleep_a_lot",
+            "play_a_lot")

> str_split(ollie, "_", simplify = TRUE)[,1] # Output matrix instead of list
[1] "eats"  "sleep" "play" 
```

### Transformation

* use `row_name_to_col` to create row index column

```R
df$ID <- rownames_to_column(.data, var = "rowname")

# another way
df$ID seq.int(nrow(df))
```
* `nest(-group)` is equivalent of `group_by(group) %>% nest()`
* `dplyr::group_split` split list of tibbles by group

```R
# Example
list <- mtcars %>% group_split(cyl)
```

* group_split combined with purrr::map_df

```R
# Another example:
first_row <- function(.x, to_chr = FALSE) {
  first <- .x[1, ]
  if (to_chr) {
    first[] <- lapply(first, as.character)
  }

  tibble::as.tibble(first)
}

mtcars %>%
  group_by(cyl) %>%
  split(group_indices(.)) %>%
  purrr::map_df(first_row, to_chr = TRUE) %>%
  ungroup()

#> # A tibble: 3 x 11
#>   mpg   cyl   disp  hp    drat  wt    qsec  vs    am    gear  carb
#>   <chr> <chr> <chr> <chr> <chr> <chr> <chr> <chr> <chr> <chr> <chr>
#> 1 22.8  4     108   93    3.85  2.32  18.61 1     1     4     1
#> 2 21    6     160   110   3.9   2.62  16.46 0     1     4     4
#> 3 18.7  8     360   175   3.15  3.44  17.02 0     0     3     2

# which is equivalent to
mtcars %>%
  split(list(.$cyl)) %>% ...

# use dplyr::group_split (group key will be `cyl`)
> mtcars %>%
  group_split(cyl) %>% ...
```

* `bind_rows`, `bind_cols`: `dplyr`'s answer to do.call(cbind, list)

```R
mtcars %>%
  filter(cyl == 8) %>%
  select(mpg,disp,hp, carb) %>%
  split(list(.$carb)) %>%
  map(~ lm(mpg ~ disp, data=.)) %>%
  map(coef) %>%
  # return named number vectors, convert to df
  map(function(x) data.frame(level = names(x), estimate = x,
                             stringsAsFactors = FALSE)) %>%
  bind_rows(.id = "carb")
```

> This is an efficient implementation of the common pattern of do.call(rbind, dfs) or do.call(cbind, dfs) for binding many data frames into one.

* dplyr::group_by takes boleans as arguments as well

```R
mtcars %>% 
  group_by(cyl > 6) %>%
  summarise(n = n())

# A tibble: 2 x 2
  `cyl > 6`     n
  <lgl>     <int>
1 FALSE        18
2 TRUE         14
```

* column selection grouping

```R
# Group by column
group_by_at(iris, vars(starts_with("Sepal")), as.factor)

# A shortcut for group_by() + mutate():
group_by_all(mtcars, as.factor)
group_by_if(iris, is.factor, as.character)
```

### Visualization

* Use rlang::{{}} for tidyeval variables inside functions!

Old way of (me) doing it to circumvent the delayed evluation by ggplot2 function...

```R
my_func <- function(df, x, y){

  ggplot(data = df, aes(x = df[[x]], y = df[[y]])) +
    geom_point()
}

#x, y must be passed in as strings
my_func(mtcars, x = "mpg", y = "cyl")
```

Now with rlang 0.4.0+, use {{var}} instead! Learnt from Sharons video on ['How to make your own ggplot2 functions'](https://www.youtube.com/watch?v=9v9-EpTuwk0)

```R
my_func <- function(df, x, y){

  ggplot(data = df, aes(x = {{x}}, y = {{y}})) +
    geom_point()
}

my_func(mtcars, mpg, cyl) # so much more convinient
```

## 4. Working in `data.table`

## 5. R Studio

`Ctrl + Shift + P` Re-Run Previous