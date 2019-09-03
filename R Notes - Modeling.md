# Modeling Tips

* model and return Rsq for each group

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
