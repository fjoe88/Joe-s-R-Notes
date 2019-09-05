# Things to learn

* `!!!`

```R
vecs <- list(
  c(1, 2, NA, NA, 5),
  c(NA, NA, 3, 4, 5)
)
coalesce(!!!vecs)
[1] 1 2 3 4 5
```

* `tidyr:separate()`

* `zoo` and `xts`
