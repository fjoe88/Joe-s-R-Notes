# Notable R Packages and Methods

## General

### zeallot
>...provides a %<-% operator to perform multiple, unpacking, and destructuring assignment in R. The operator unpacks the right-hand side of an assignment into multiple values and assigns these values to variables on the left-hand side of the assignment.

### glue
>...offers interpreted string literals that are small, fast, and dependency-free. Glue does this by embedding R expressions in curly braces which are then evaluated and inserted into the argument string.

### datapasta
>RStudio addins and R functions that make copy-pasting vectors and tables to text painless.

* [Official Document](https://github.com/tidyverse/glue)

## Tidy Data (Hadleyverse)

### padr

>...aims to make light work of preparing time series data by offering the two main functions thicken and pad. A small example before we get into detail. Say I want to make a line plot of my daily expenses at the coffee place. The data for a few days might look like.

* [Padr Intro](https://edwinth.github.io/blog/padr-intro/)
## Modeling

### caret
  * [Max Kuhn's Own Manual](http://topepo.github.io/caret/index.html)

### parsnip

>...provides functions and methods to create and manipulate functions commonly used during modeling (e.g. fitting the model, making predictions, etc). It allows the user to manipulate how the same type of model can be created from different sources. It also contains a basic framework for model parameter tuning.

* [Parsnip Intro](https://tidymodels.github.io/parsnip/articles/parsnip_Intro.html)

### dials

>...contains tools to create and manage values of tuning parameters and is designed to integrate well with the parsnip package.

### olsrr

>...a set of tools for improved output from linear regression models, designed keeping in mind beginner/intermediate R users.

## Visualization

### ggrepel

>ggrepel provides geoms for ggplot2 to repel overlapping text labels