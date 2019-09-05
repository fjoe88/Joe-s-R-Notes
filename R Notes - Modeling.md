# Modeling Tips

## Code Snippets

### Return Model Rsq for Each Group

```R
surveys %>%
  filter(taxa == "Rodent",
         !is.na(weight)) %>%
  select(genus,weight,sex) %>%
  split(.$genus) %>%
  map(~ lm(weight ~ sex, data=.)) %>%
  map(summary) %>%
  map_dbl("r.squared")

##         Baiomys     Chaetodipus       Dipodomys         Neotoma
##     0.164529171     0.003305788     0.001992282     0.016659203
##       Onychomys     Perognathus      Peromyscus Reithrodontomys
##     0.002306456     0.003560761     0.042744223     0.054463691
##        Sigmodon    Spermophilus
##     0.038990704     1.000000000
```

## Workflows

### Feature Selection

1. Removing features with low variance¶

* [Feature Selection Approach in R](http://r-statistics.co/Variable-Selection-and-Importance-With-R.html)