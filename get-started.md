Get started with fgeo
================

This is an R Markdown document (<http://rmarkdown.rstudio.com>). When
you click the the green arrow on the top-right of each code chunk, R
runs that code.

This article shows some of the key features of **fgeo** applied to an
exploratory data analysis. For a deeper and general approach to
exploratory data analysis see [this book
section](http://r4ds.had.co.nz/exploratory-data-analysis.html). A
version adapted for ForestGEO is available
[here](https://forestgeo.github.io/fgeo/articles/siteonly/eda.html).

## Packages

In every new R session you need to “open” **fgeo** with `library`().

``` r
library(fgeo)
#> ── Attaching packages ──────────────────────────────────────────── fgeo 0.0.0.9000 ──
#> ✔ bciex           0.0.0.9000     ✔ fgeo.demography 0.0.0.9000
#> ✔ fgeo.abundance  0.0.0.9004     ✔ fgeo.habitat    0.0.0.9006
#> ✔ fgeo.base       0.0.0.9001     ✔ fgeo.map        0.0.0.9204
#> ✔ fgeo.data       0.0.0.9002     ✔ fgeo.tool       0.0.0.9003
#> Warning in fun(libname, pkgname): couldn't connect to display ":0"
#> ── Conflicts ──────────────────────────────────────────────────── fgeo_conflicts() ──
#> ✖ fgeo.tool::filter() masks stats::filter()
```

## Data

You may load your own data. Here I’ll use data from the **fgeo.data**
package – which comes with **fgeo**.

``` r
str(fgeo_index_datasets())
#> 'data.frame':    46 obs. of  2 variables:
#>  $ package: chr  "bciex" "bciex" "bciex" "bciex" ...
#>  $ dataset: chr  "bci_elevation" "bci_habitat" "bci_mat" "bci_plotdim" ...
```

I’ll use a dataset of stems censused in one hectare in the forest plot
Luquillo, Puerto Rico
(<https://forestgeo.si.edu/sites/north-america/luquillo>).

``` r
stem <- luquillo_stem_1ha
str(stem)
#> Classes 'tbl_df', 'tbl' and 'data.frame':    72618 obs. of  19 variables:
#>  $ treeID   : int  46 47 47 47 47 47 47 47 47 47 ...
#>  $ stemID   : int  46 47 48 49 50 51 52 53 54 55 ...
#>  $ tag      : chr  "100001" "100008" "100008" "100008" ...
#>  $ StemTag  : chr  "100001" "100002" "100003" "100004" ...
#>  $ sp       : chr  "PSYBRA" "PSYBRA" "PSYBRA" "PSYBRA" ...
#>  $ quadrat  : chr  "921" "921" "921" "921" ...
#>  $ gx       : num  164 165 165 165 165 ...
#>  $ gy       : num  416 416 416 416 416 ...
#>  $ MeasureID: int  46 47 48 49 50 51 52 53 54 55 ...
#>  $ CensusID : int  1 1 1 1 1 1 1 1 1 1 ...
#>  $ dbh      : num  22.6 15 12.8 10.9 13.4 10.8 15.4 16.6 13.3 11.1 ...
#>  $ pom      : chr  "1.3" "1.3" "1.3" "1.3" ...
#>  $ hom      : num  1.3 1.3 1.3 1.3 1.3 1.3 1.3 1.3 1.3 1.3 ...
#>  $ ExactDate: num  8610 8610 8610 8610 8610 8610 8610 8610 8610 8610 ...
#>  $ DFstatus : chr  "alive" "alive" "alive" "alive" ...
#>  $ codes    : chr  "MAIN;A" "SPROUT;A" "SPROUT;A" "SPROUT;A" ...
#>  $ countPOM : num  1 1 1 1 1 1 1 1 1 1 ...
#>  $ status   : chr  "A" "A" "A" "A" ...
#>  $ date     : num  NA NA NA NA NA NA NA NA NA NA ...
```

For a description of the columns see `?data_dictionary`.

``` r
str(data_dictionary)
#> Classes 'tbl_df', 'tbl' and 'data.frame':    242 obs. of  3 variables:
#>  $ table      : chr  "Census" "Census" "Census" "Census" ...
#>  $ column     : chr  "CensusID" "PlotID" "PlotCensusNumber" "StartDate" ...
#>  $ description: chr  "Primary key, an integer  automatically generated to uniquely identify a census." "Foreign Key to Site table." "Integer census number for an individual plot, 1=first census, 2=second census, etc. If there are more than one "| __truncated__ "Date on which the first measurement of the census was taken." ...

cols <- names(stem)
subset(data_dictionary, column %in% cols)
#> # A tibble: 20 x 3
#>    table                 column    description                            
#>    <chr>                 <chr>     <chr>                                  
#>  1 Census                CensusID  Primary key, an integer  automatically…
#>  2 CensusQuadrat         CensusID  Foreign Key to Census table.           
#>  3 DataCollection        CensusID  Foreign Key to Census table.           
#>  4 DBH                   CensusID  Foreign Key to Census table.           
#>  5 DBH                   ExactDate Date on which the measurement was take…
#>  6 DBHAttributes         CensusID  Foreign Key to Census table.           
#>  7 Measurement           MeasureID Primary key, an integer  automatically…
#>  8 Measurement           CensusID  Foreign Key to Census table.           
#>  9 Measurement           ExactDate "Date on which measurement has been do…
#> 10 MeasurementAttributes MeasureID Foreign Key to Measurement table.      
#> 11 MeasurementAttributes CensusID  Foreign Key to Census table.           
#> 12 RemeasAttribs         CensusID  Foreign Key to Census table.           
#> 13 Remeasurement         CensusID  Foreign Key to  Census table.          
#> 14 Remeasurement         ExactDate "Date of remeasurement.  (format is yy…
#> 15 SpeciesInventory      CensusID  Foreign Key to  Census table.          
#> 16 Stem                  StemTag   The stem tag used in the field to iden…
#> 17 TreeAttributes        CensusID  Foreign Key to  Census table.          
#> 18 ViewFullTable         StemTag   The stem tag used in the field to iden…
#> 19 ViewFullTable         CensusID  Foreign Key to  Census table.          
#> 20 ViewFullTable         ExactDate Date on which the measurement was take…
```

This dataset comes with multiple censuses. I’ll pick only the latest
one.

``` r
unique(stem$CensusID)
#> [1] 1 2 3 4 5 6

# `pick_top()` with n < 0 from the bottom rank of `var`. See ?pick_top().
stem6 <- pick_top(stem, var = CensusID, n = -1)
unique(stem6$CensusID)
#> [1] 6
```

## Exploring the distribution of status and tree diameter

Two columns that are commonly useful in ForestGEO datasets are `status`
and `dbh` (diameter at breast height). Let’s begin by understanding what
type of variables they are. For this, base R provides useful functions.

`status` is a categorical variable.

``` r
summary(stem6$status)
#>    Length     Class      Mode 
#>     12103 character character
```

We can count the number of observations in each category with `table()`,
then visualize the result with `barplot()`.

``` r
by_category <- table(stem6$status)
barplot(by_category)
```

![](get-started_files/figure-gfm/barplot-1.png)<!-- -->

`dbh` is a continuous numeric variable.

``` r
summary(stem6$dbh)
#>    Min. 1st Qu.  Median    Mean 3rd Qu.    Max.    NA's 
#>   10.00   21.77   56.00   93.71  142.00 1405.00    9539
```

(Note the missing values (`NA`’s).)

And we can visualize its distribution with `hist()`.

``` r
hist(stem6$dbh)
```

![](get-started_files/figure-gfm/histogram-1.png)<!-- -->

Unfortunately `hist()` dropped missing values silently. But we can
better understand how missing values of `dbh` relate to `status` by
extracting only the columns `dbh` and `status`, and picking only the
rows where `dbh` is missing.

``` r
dbh_status <- stem6[c("dbh", "status")]
missing <- subset(dbh_status, is.na(dbh))
unique(missing)
#> # A tibble: 3 x 2
#>     dbh status
#>   <dbl> <chr> 
#> 1    NA D     
#> 2    NA G     
#> 3    NA A
```

Another approach is to count missing values.

``` r
missing <- transform(stem6, na = ifelse(is.na(dbh), TRUE, FALSE))
table(missing$na, missing$status)
#>        
#>            A    D    G
#>   FALSE 2564    0    0
#>   TRUE     4 5416 4119
```

We learn that `dbh` is missing where a tree is dead (`status = A`) or
gone (`status = G`). This makes sense and, depending on the type of
analysis we want to do, we may want to keep or remove missing values.

## Determining tree status based on stem status

Now that we understand out data are read to clean it, for example, by
picking alive trees only. At ForestGEO, working with `status` is so
common that **fgeo** provides a specialized function.

``` r
fgeo_help("status")
```

``` r
fgeo_funs <- fgeo_index_functions()
subset(fgeo_funs, grepl("status", fun))
#>       package             fun
#> 49  fgeo.base     drop_status
#> 69  fgeo.base     pick_status
#> 123 fgeo.tool add_status_tree
#> 143 fgeo.tool   filter_status
```

In `stem6`, the variable `status` records the status of each individual
stem. How can we determine the status of a tree based on the status of
each of its stems? That is the job of `add_status_tree()`.

``` r
stem6 <- add_status_tree(stem6, status_a = "A", status_d = "D")
alive_trees <- subset(stem6, status_tree == "A")

# Note that alive trees may have some missing, gone or dead stems
some_cols <- c( "treeID", "status_tree", "stemID", "status")
example_tree <- 46
subset(alive_trees, treeID == example_tree, some_cols)
#> # A tibble: 2 x 4
#>   treeID status_tree stemID status
#>    <int> <chr>        <int> <chr> 
#> 1     46 A               46 D     
#> 2     46 A           114033 G
```

## Picking a `dbh` range

Another very common task when working with ForestGEO data is to pick
stems of a particular `dbh` range.

``` r
fgeo_help("dbh")
```

``` r
subset(fgeo_funs, grepl("dbh", fun))
#>      package            fun
#> 64 fgeo.base   pick_dbh_max
#> 65 fgeo.base   pick_dbh_min
#> 66 fgeo.base  pick_dbh_over
#> 67 fgeo.base pick_dbh_under
```

Pick stems of 10 mm or more.

``` r
ten_plus <- pick_dbh_min(alive_trees, 10)
range(ten_plus$dbh, na.rm = TRUE)
#> [1]   10 1405
```

## Dropping missing values

Drop missing values of `dbh` with an informative warning.

``` r
non_missing <- drop_if_na(ten_plus, "dbh")
#> Warning: Dropping 5261 rows with missing `dbh` values.
```

## Calculating abundance

Calculate the total abundance of stems and trees.

``` r
abundance_stem(non_missing)
#> # A tibble: 1 x 1
#>       n
#>   <int>
#> 1  2564

abundance_tree(non_missing)
#> # A tibble: 1 x 1
#>       n
#>   <int>
#> 1  2319
```

Calculate the abundance of trees by species.

``` r
by_sp <- group_by(non_missing, sp)
n_by_sp <- abundance_tree(by_sp)
n_by_sp
#> # A tibble: 70 x 2
#>    sp         n
#>    <chr>  <int>
#>  1 ALCFLO    11
#>  2 ALCLAT    15
#>  3 ANDINE     1
#>  4 ANTOBT     1
#>  5 ARDGLA     1
#>  6 BUCTET    11
#>  7 BYRSPI    25
#>  8 CALCAL     2
#>  9 CASARB   489
#> 10 CASSYL    58
#> # ... with 60 more rows
```

Above you saw that `abundance_tree(DATA)` and `abundance_stem(DATA)`
generalize to `count_distinct(DATA, stemID)` and `count_distinct(DATA,
stemID)`. An even greater generalization is `count()`. **fgeo** borrows
the function `count()` (and some friends) from the
[**dplyr**](https://dplyr.tidyverse.org/) package. Today,
`dplyr::count()` appears to be the simplest, the most general and
powerful tool to count things. **fgeo** imports and reexports
`dplyr::count()` (and friends) so it’s available when you run
`library(fgeo)`.

``` r
fgeo_help("reexports", package = "fgeo.abundance")
```

## Picking the most abundant species

Which are the three most abundant tree species?

``` r
top3 <- pick_top(n_by_sp, n, -3)
top3
#> # A tibble: 3 x 2
#>   sp         n
#>   <chr>  <int>
#> 1 CASARB   489
#> 2 PREMON   507
#> 3 SCHMOR   151
```

Now we can subset the `alive_trees` of only the `top3` species.

``` r
picked_stems <- subset(alive_trees, sp %in% top3$sp)
```

## Mapping the distribution of tree species

**fgeo** includes some functions specialized in mapping ForestGEO’s
data.

``` r
fgeo_help("map")
```

``` r
subset(fgeo_funs, grepl("map", fun))
#>      package             fun
#> 100 fgeo.map        map_elev
#> 101 fgeo.map       map_gx_gy
#> 102 fgeo.map  map_gx_gy_elev
#> 103 fgeo.map map_quad_header
#> 104 fgeo.map     map_sp_elev
#> 105 fgeo.map  map_tag_header
#> 106 fgeo.map      maply_quad
#> 107 fgeo.map   maply_sp_elev
#> 108 fgeo.map       maply_tag
#> 111 fgeo.map  theme_map_quad
#> 112 fgeo.map   theme_map_tag
```

Map the most abundant species.

``` r
# luquillo_elevation comes with fgeo
p <- map_sp_elev(picked_stems, elevation = luquillo_elevation, point_size = 1)
p
```

![](get-started_files/figure-gfm/species-distribution1-1.png)<!-- -->

Tweak to focus on the hectare available in the data.

``` r
p1 <- limit_gx_gy(p, xlim = c(100, 200), ylim = c(400, 500))
#> Scale for 'x' is already present. Adding another scale for 'x', which
#> will replace the existing scale.
#> Scale for 'y' is already present. Adding another scale for 'y', which
#> will replace the existing scale.
p1
```

![](get-started_files/figure-gfm/species-distribution2-1.png)<!-- -->

## Determine species-habitat associations

Let’s determine the species-habitat associations using a torus
translation test.

``` r
fgeo_help("torus")
```

This test should use individual trees not the (potentially multiple)
stems of individual trees. This test only makes sense at the population
level. We are interested in knowing whether or not individuals of a
species are aggregated on a habitat. Multiple stems of an individual do
not represent population level processes but individual level processes.
Let’s then use a ForestGEO *tree* table. I’ll use
`luquillo_tree6_random`, which contains a small sample of randomly
chosen trees across the entire plot (see `?`luquillo\_tree6\_random\`).

``` r
tree <- luquillo_tree6_random
```

I’ll pick alive trees of 10 mm or more (for details see previous
sections). The variable `status` of ‘tree’ tables directly represent the
status of each tree (see `?census_description`). I’ll focus on trees
with status “A” (alive).

``` r
dbh10plus <- pick_dbh_min(tree, 10)
chosen_trees <- pick_status(dbh10plus, "A")
unique(chosen_trees$status)
#> [1] "A"
```

Note that `tt_test()` is recommended for sufficiently abundant species:

> You should only try to determine the habitat association for
> sufficiently abundant species - in a 50-ha plot, a minimum abundance
> of 50 trees/species has been used.

– `?tt_test()`

``` r
# Find sufficiently abundant species
by_sp <- group_by(tree, sp)
n_by_sp <- abundance_tree(by_sp)
n_by_sp
#> # A tibble: 73 x 2
#>    sp         n
#>    <chr>  <int>
#>  1 ALCFLO     3
#>  2 ALCLAT     1
#>  3 ANDINE     2
#>  4 ARDGLA     2
#>  5 ARTALT     1
#>  6 BRUPOR     1
#>  7 BUCTET     8
#>  8 BYRSPI    11
#>  9 CALCAL     1
#> 10 CASARB    94
#> # ... with 63 more rows

n_sp50plus <- subset(n_by_sp, n > 50)
n_sp50plus
#> # A tibble: 3 x 2
#>   sp         n
#>   <chr>  <int>
#> 1 CASARB    94
#> 2 PREMON   256
#> 3 SLOBER    84
```

We’ll need habitat data. Or at least elevation data – with which to
create habitat data with `fgeo_habitat()`.

``` r
# See ?luquillo_habitat
habitat <- luquillo_habitat
# Same
habitat <- fgeo_habitat(luquillo_elevation, gridsize = 20, n = 4)
str(habitat)
#> Classes 'fgeo_habitat', 'tbl_df', 'tbl' and 'data.frame':    6400 obs. of  3 variables:
#>  $ gx      : int  0 0 0 0 0 0 0 0 0 0 ...
#>  $ gy      : int  0 5 10 15 20 25 30 35 40 45 ...
#>  $ habitats: int  1 1 1 1 1 1 1 1 1 1 ...
```

And now we are ready to run the test.

``` r
tt <- tt_test(chosen_trees, sp = n_sp50plus$sp, habitat = habitat)
```

The output of `tt_test()` is a list. Large lists are awkward to view –
unless you use `View()` in RStudio.

``` r
View(tt)
```

Alternatively, transform the list to a dataframe.

``` r
tt_df <- to_df(tt)
# Nice view (try also `View()`)
as_tibble(tt_df)
#> # A tibble: 72 x 3
#>    metric         sp         value
#>    <chr>          <chr>      <dbl>
#>  1 N.Hab.1        CASARB    28    
#>  2 Gr.Hab.1       CASARB 25511    
#>  3 Ls.Hab.1       CASARB    86    
#>  4 Eq.Hab.1       CASARB     3    
#>  5 Rep.Agg.Neut.1 CASARB     1    
#>  6 Obs.Quantile.1 CASARB     0.997
#>  7 N.Hab.2        CASARB    12    
#>  8 Gr.Hab.2       CASARB  3691    
#>  9 Ls.Hab.2       CASARB 21892    
#> 10 Eq.Hab.2       CASARB    17    
#> # ... with 62 more rows
```

## Krige soil data

Let’s Krige soil data.

``` r
fgeo_help("krig")
```

**fgeo** provides a fake soil dataset for examples.

``` r
str(soil_fake)
#> Classes 'tbl_df', 'tbl' and 'data.frame':    30 obs. of  5 variables:
#>  $ gx: int  40 56 61 67 113 173 239 257 283 294 ...
#>  $ gy: int  193 30 102 110 16 134 252 442 288 181 ...
#>  $ mg: num  0.67 0.5 0.65 0.5 0.56 0.74 0.47 0.52 0.45 0.7 ...
#>  $ c : num  1.75 2.25 2.05 2.35 1.45 1.55 0.85 2.25 0.45 2.45 ...
#>  $ p : num  6.5 5.9 6.4 6.4 6.2 7 5.9 6.2 6.7 7.1 ...
```

The data contains multiple soil variables; let’s use only two of them.

``` r
soil_vars <- c("c", "p")
krig_list <- krig(soil_fake, soil_vars, quiet = TRUE)
#> Warning in duplicated.matrix(res$coords, MAR = 1): partial argument match
#> of 'MAR' to 'MARGIN'
#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'
#> Warning in duplicated.matrix(res$coords, MAR = 1): partial argument match
#> of 'MAR' to 'MARGIN'
#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'

#> Warning: partial match of 'weight' to 'weights'
```

The output is a nested list, which is awkward to view on the console but
you can view it in RStudio with `View()` and summarize it with
`summary()`

``` r
summary(krig_list)
#> var: c 
#> df
#> 'data.frame':    1150 obs. of  3 variables:
#>  $ x: num  10 30 50 70 90 110 130 150 170 190 ...
#>  $ y: num  10 10 10 10 10 10 10 10 10 10 ...
#>  $ z: num  2.13 2.12 2.1 2.09 2.07 ...
#> 
#> df.poly
#> 'data.frame':    1150 obs. of  3 variables:
#>  $ gx: num  10 30 50 70 90 110 130 150 170 190 ...
#>  $ gy: num  10 10 10 10 10 10 10 10 10 10 ...
#>  $ z : num  2.13 2.12 2.1 2.09 2.07 ...
#> 
#> lambda
#> 'numeric'
#>  num 1
#> 
#> vg
#> 'variogram'
#> List of 20
#>  $ u               : num [1:9] 60.9 86.5 103 122.7 146.1 ...
#>  $ v               : num [1:9] 0.284 0.422 0.882 0.543 0.211 ...
#>  $ n               : num [1:9] 7 9 10 10 18 19 36 34 38
#>  $ sd              : num [1:9] 0.414 0.48 0.633 0.501 0.405 ...
#>  $ bins.lim        : num [1:31] 1.00e-12 2.00 2.38 2.84 3.38 ...
#>  $ ind.bin         : logi [1:30] FALSE FALSE FALSE FALSE FALSE FALSE ...
#>  $ var.mark        : num 0.317
#>  $ beta.ols        : num -1.2e-09
#>  $ output.type     : chr "bin"
#>  $ max.dist        : num 320
#>  $ estimator.type  : chr "classical"
#>  $ n.data          : int 30
#>  $ lambda          : num 1
#>  $ trend           : chr "cte"
#>  $ pairs.min       : num 5
#>  $ nugget.tolerance: num 1e-12
#>  $ direction       : chr "omnidirectional"
#>  $ tolerance       : chr "none"
#>  $ uvec            : num [1:30] 1 2.19 2.61 3.11 3.7 ...
#>  $ call            : language variog(geodata = geodata, breaks = breaks, trend = trend, pairs.min = 5)
#> 
#> vm
#> 'variomodel', variofit'
#> List of 17
#>  $ nugget               : num 0.352
#>  $ cov.pars             : num [1:2] 0 160
#>  $ cov.model            : chr "exponential"
#>  $ kappa                : num 0.5
#>  $ value                : num 4.64
#>  $ trend                : chr "cte"
#>  $ beta.ols             : num -1.2e-09
#>  $ practicalRange       : num 480
#>  $ max.dist             : num 320
#>  $ minimisation.function: chr "optim"
#>  $ weights              : chr "npairs"
#>  $ method               : chr "WLS"
#>  $ fix.nugget           : logi FALSE
#>  $ fix.kappa            : logi TRUE
#>  $ lambda               : num 1
#>  $ message              : chr "optim convergence code: 0"
#>  $ call                 : language variofit(vario = vg, ini.cov.pars = c(initialVal, startRange), cov.model = varModels[i],      nugget = initialVal)
#> 
#> var: p 
#> df
#> 'data.frame':    1150 obs. of  3 variables:
#>  $ x: num  10 30 50 70 90 110 130 150 170 190 ...
#>  $ y: num  10 10 10 10 10 10 10 10 10 10 ...
#>  $ z: num  6.37 6.35 6.33 6.31 6.29 ...
#> 
#> df.poly
#> 'data.frame':    1150 obs. of  3 variables:
#>  $ gx: num  10 30 50 70 90 110 130 150 170 190 ...
#>  $ gy: num  10 10 10 10 10 10 10 10 10 10 ...
#>  $ z : num  6.37 6.35 6.33 6.31 6.29 ...
#> 
#> lambda
#> 'numeric'
#>  num 1
#> 
#> vg
#> 'variogram'
#> List of 20
#>  $ u               : num [1:9] 60.9 86.5 103 122.7 146.1 ...
#>  $ v               : num [1:9] 0.396 0.4 0.11 0.402 0.385 ...
#>  $ n               : num [1:9] 7 9 10 10 18 19 36 34 38
#>  $ sd              : num [1:9] 0.592 0.414 0.133 0.409 0.488 ...
#>  $ bins.lim        : num [1:31] 1.00e-12 2.00 2.38 2.84 3.38 ...
#>  $ ind.bin         : logi [1:30] FALSE FALSE FALSE FALSE FALSE FALSE ...
#>  $ var.mark        : num 0.267
#>  $ beta.ols        : num -2.21e-09
#>  $ output.type     : chr "bin"
#>  $ max.dist        : num 320
#>  $ estimator.type  : chr "classical"
#>  $ n.data          : int 30
#>  $ lambda          : num 1
#>  $ trend           : chr "cte"
#>  $ pairs.min       : num 5
#>  $ nugget.tolerance: num 1e-12
#>  $ direction       : chr "omnidirectional"
#>  $ tolerance       : chr "none"
#>  $ uvec            : num [1:30] 1 2.19 2.61 3.11 3.7 ...
#>  $ call            : language variog(geodata = geodata, breaks = breaks, trend = trend, pairs.min = 5)
#> 
#> vm
#> 'variomodel', variofit'
#> List of 17
#>  $ nugget               : num 0.305
#>  $ cov.pars             : num [1:2] 0 160
#>  $ cov.model            : chr "exponential"
#>  $ kappa                : num 0.5
#>  $ value                : num 0.818
#>  $ trend                : chr "cte"
#>  $ beta.ols             : num -2.21e-09
#>  $ practicalRange       : num 479
#>  $ max.dist             : num 320
#>  $ minimisation.function: chr "optim"
#>  $ weights              : chr "npairs"
#>  $ method               : chr "WLS"
#>  $ fix.nugget           : logi FALSE
#>  $ fix.kappa            : logi TRUE
#>  $ lambda               : num 1
#>  $ message              : chr "optim convergence code: 0"
#>  $ call                 : language variofit(vario = vg, ini.cov.pars = c(initialVal, startRange), cov.model = varModels[i],      nugget = initialVal)
```

Also you can pull a dataframe containing the results of all the soil
variables.

``` r
krig_df <- to_df(krig_list, item = "df")
as_tibble(subset(krig_df,var == "c"))
#> # A tibble: 1,150 x 4
#>    var       x     y     z
#>  * <fct> <dbl> <dbl> <dbl>
#>  1 c        10    10  2.13
#>  2 c        30    10  2.12
#>  3 c        50    10  2.10
#>  4 c        70    10  2.09
#>  5 c        90    10  2.07
#>  6 c       110    10  2.06
#>  7 c       130    10  2.04
#>  8 c       150    10  2.03
#>  9 c       170    10  2.01
#> 10 c       190    10  2.00
#> # ... with 1,140 more rows

as_tibble(subset(krig_df, var == "p"))
#> # A tibble: 1,150 x 4
#>    var       x     y     z
#>  * <fct> <dbl> <dbl> <dbl>
#>  1 p        10    10  6.37
#>  2 p        30    10  6.35
#>  3 p        50    10  6.33
#>  4 p        70    10  6.31
#>  5 p        90    10  6.29
#>  6 p       110    10  6.27
#>  7 p       130    10  6.26
#>  8 p       150    10  6.24
#>  9 p       170    10  6.23
#> 10 p       190    10  6.21
#> # ... with 1,140 more rows
```

# Demography

``` r
fgeo_help("mortality")
```

Demography functions (`recruitment()`, `mortality()` and `growth()`)
input two censuses. We’ll use small *tree* tables from census 6 and 7 of
Barro Colorado Island.

``` r
# ?see bciex::bci12t6mini
census1 <- bci12t6mini
census2 <- bci12t7mini
```

`recruitment()`, `mortality()` and `growth()` work similarly. The first
and second input are the earlier and later censuses you want to analyze
demography on.

``` r
mortality(census1, census2)
#> $N
#> [1] 596
#> 
#> $D
#> [1] 84
#> 
#> $rate
#> [1] 0.03056828
#> 
#> $lower
#> [1] 0.02465888
#> 
#> $upper
#> [1] 0.03778665
#> 
#> $time
#> [1] 4.969727
#> 
#> $date1
#> [1] 16578.29
#> 
#> $date2
#> [1] 18393.49
#> 
#> $dbhmean
#> [1] 49.41016
```

All three demography functions provide arguments to split the data by.
This is a different approach to do the same job of `group_by()` shown
above (here `group_by()` doesn’t work).

``` r
recruitment <- recruitment(census1, census2, split1 = census1$sp)
#> Warning in check_if_all_dbh_is_na(census1, census2, split = split1): At least one split-group contains all `dbh` values equal to NA.
#>   * Consider removing those groups before running this function.
```

If you “split” the data, the output is a somewhat long list. The best
way to view it is with `View()` in RStudio.

``` r
View(recruitment)
```

Alternatively use the `*_df()` variants of demography. They output a
dataframe that is easier to handle – it prints particularly well with
`as_tibble()`.

``` r
growth <- growth_df(census1, census2, split1 = census1$sp)
#> Warning in check_if_all_dbh_is_na(census1, census2, split = split1): At least one split-group contains all `dbh` values equal to NA.
#>   * Consider removing those groups before running this function.
as_tibble(growth)
#> # A tibble: 1,008 x 3
#>    split  metric      value
#>    <chr>  <chr>       <dbl>
#>  1 acaldi rate        0.709
#>  2 acaldi N           2    
#>  3 acaldi clim        1.32 
#>  4 acaldi dbhmean    11    
#>  5 acaldi time        4.95 
#>  6 acaldi date1   16670.   
#>  7 acaldi date2   18477    
#>  8 aegipa rate       NA    
#>  9 aegipa N           0    
#> 10 aegipa clim       NA    
#> # ... with 998 more rows
```

Here too you can use `View()` and search for the species you want. Or
you can `subset()` the output and print it to the console.

``` r
subset(growth, split == "acaldi")
#> # A tibble: 7 x 3
#>   split  metric      value
#>   <chr>  <chr>       <dbl>
#> 1 acaldi rate        0.709
#> 2 acaldi N           2    
#> 3 acaldi clim        1.32 
#> 4 acaldi dbhmean    11    
#> 5 acaldi time        4.95 
#> 6 acaldi date1   16670.   
#> 7 acaldi date2   18477
```

If you are interested in only a few species (say less than 5), you may
want to spread the species from rows to columns with `spread()` from the
**tidyr** package (see `?tidyr::spread()`).
