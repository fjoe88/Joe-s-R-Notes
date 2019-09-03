# Useful Functions

## Existing Methods

* `dplyr::coelesce`

## Function Snippets

* coalesce which returns the first non null column element for each row

```R
coalesce <- function(...) {
Reduce(function(x, y) {
    i <- which(is.na(x))
    x[i] <- y[i]
    x},
list(...))
}

#Example
a <- c(1,  2,  NA, 4, NA)
b <- c(NA, NA, NA, 5, 6)
c <- c(7,  8,  NA, 9, 10)
coalesce(a,b,c)
# [1]  1  2 NA  4  6
```