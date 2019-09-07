# Tips and Tricks

## Use base R is classy

> base R function that does the job and does it well and efficient, thats classy! (like my golden retriever, Ollie)

![Ollie_classy](https://lh3.googleusercontent.com/1aPsRwgsiww4c_nN14uYMjTxK6x_skdNDhlzXGHLKT0lMoiAko8AED-8FO3kohwvypNNB6Yxy5tU5ZbAmfybXnrR02F7QLfLiuUx5U5FFj9kobN9H9qK3o3zkRrCiXlwhUAoBfbPSed-vkrZyyuUXXd1APAFOoxrwXd04RS0pOlsWN1lThgrqVnJchabKZTx4zxcEtUIspkjAD0WdWjMtTK1N9Rul1Pru1jeLs0daMYnyWU0k-5g4bEjqK4_J4ECCPv38keL5oKggfpD7Oi4qwPz46mt6Rf8SfaSepKua5Xyc3SAVt5O0b4FiEova2dXG4E91MI8IJvz-CJvQZmNxUNNqPUF1GhUyJxeSGnG5hT_erER0ajvjoTPC_xNbVlPS_2QySBddDtfeMi_Dmqw1Gw7_jL1rn4PmDZvN_bCjp0UiWopu7_X_SKULfXZ7tJEmkVWKnLkJSRiTfr-nSt8EQ-gEbIltA8o7ceHPV_tdWspY-KtudIKIzWHnqcJOwywG2qWBZbe5LD7sFXIYf9pzVQPqCEdlaWpcBaNlXwL1U2nfkjzfVV0R0flEt6Ak7PRWzv3LmyaR1jw-N6DEQnKWqsd5GCxPe8pH4jA0jchlHPVUlUvxR2kfSub4-31FqHU_9IfaXjZhIiEPOaEdvo7qJRjZ_0i4g4acn_NNCEYZaIbnJtsY19tlkE=w150-h170-no)

* Sampling

```R
ind <- sample(2, nrow(df), replace = TRUE, prob=c(0.3, 0.7))
train <- df[ind==1, ]
test <- df[ind==2, ]
```

## Transformation

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

## Visualization

* Use rlang::{{}} for tidyeval variables inside functions!

> Old way I was doing of circumvent the delayed evluation by ggplot2 function

```R
require(rlang)
require(tidyverse)


my_func <- function(df, x, y){
  #x, y must be "string" format
  ggplot(data = df, aes(x = df[[x]], y = df[[y]])) +
    geom_point()
}

my_func(mtcars, mpg, cyl)
```

> Now with rlang 0.4.0+, use {{var}} instead! Learnt from Sharons video on ['How to make your own ggplot2 functions'](https://www.youtube.com/watch?v=9v9-EpTuwk0)

```R
require(rlang)
require(tidyverse)


my_func <- function(df, x, y){
  #x, y must be "string" format
  ggplot(data = df, aes(x = {{x}}, y = {{y}}) +
    geom_point()
}

my_func(mtcars, mpg, cyl)
```