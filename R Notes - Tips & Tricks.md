# Tips and Tricks

* sampling (one way of doing it)

```R
ind <- sample(2, nrow(df), replace = TRUE, prob=c(0.3, 0.7))
train <- df[ind==1, ]
test <- df[ind==2, ]
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
