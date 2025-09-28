---
title: "fertility"
---


::: {.cell}

```{.r .cell-code}
library(tidyverse)
```

::: {.cell-output .cell-output-stderr}

```
── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
✔ dplyr     1.1.4     ✔ readr     2.1.5
✔ forcats   1.0.0     ✔ stringr   1.5.2
✔ ggplot2   4.0.0     ✔ tibble    3.3.0
✔ lubridate 1.9.4     ✔ tidyr     1.3.1
✔ purrr     1.1.0     
── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
✖ dplyr::filter() masks stats::filter()
✖ dplyr::lag()    masks stats::lag()
ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors
```


:::

```{.r .cell-code}
library(mosaic) # Our all-in-one package
```

::: {.cell-output .cell-output-stderr}

```
Registered S3 method overwritten by 'mosaic':
  method                           from   
  fortify.SpatialPolygonsDataFrame ggplot2

The 'mosaic' package masks several functions from core packages in order to add 
additional features.  The original behavior of these functions should not be affected by this.

Attaching package: 'mosaic'

The following object is masked from 'package:Matrix':

    mean

The following objects are masked from 'package:dplyr':

    count, do, tally

The following object is masked from 'package:purrr':

    cross

The following object is masked from 'package:ggplot2':

    stat

The following objects are masked from 'package:stats':

    binom.test, cor, cor.test, cov, fivenum, IQR, median, prop.test,
    quantile, sd, t.test, var

The following objects are masked from 'package:base':

    max, mean, min, prod, range, sample, sum
```


:::

```{.r .cell-code}
library(skimr) # Looking at data
```

::: {.cell-output .cell-output-stderr}

```

Attaching package: 'skimr'

The following object is masked from 'package:mosaic':

    n_missing
```


:::

```{.r .cell-code}
library(visdat) # Mapping missing data
library(naniar) # Missing data visualization and munging
```

::: {.cell-output .cell-output-stderr}

```

Attaching package: 'naniar'

The following object is masked from 'package:skimr':

    n_complete
```


:::

```{.r .cell-code}
library(janitor) # Clean the data
```

::: {.cell-output .cell-output-stderr}

```

Attaching package: 'janitor'

The following objects are masked from 'package:stats':

    chisq.test, fisher.test
```


:::

```{.r .cell-code}
library(tinytable) # Printing Tables for our data
```

::: {.cell-output .cell-output-stderr}

```

Attaching package: 'tinytable'

The following object is masked from 'package:ggplot2':

    theme_void
```


:::
:::



::: {.cell}

```{.r .cell-code}
fertility <- readr::read_csv("Data/Fertility.csv")
```

::: {.cell-output .cell-output-stderr}

```
Rows: 254654 Columns: 9
── Column specification ────────────────────────────────────────────────────────
Delimiter: ","
chr (6): morekids, gender1, gender2, afam, hispanic, other
dbl (3): rownames, age, work

ℹ Use `spec()` to retrieve the full column specification for this data.
ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```


:::
:::


## Glimpsing Fertility


::: {.cell}

```{.r .cell-code}
dplyr::glimpse(fertility)
```

::: {.cell-output .cell-output-stdout}

```
Rows: 254,654
Columns: 9
$ rownames <dbl> 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18…
$ morekids <chr> "no", "no", "no", "no", "no", "no", "no", "no", "no", "no", "…
$ gender1  <chr> "male", "female", "male", "male", "female", "male", "female",…
$ gender2  <chr> "female", "male", "female", "female", "female", "female", "ma…
$ age      <dbl> 27, 30, 27, 35, 30, 26, 29, 33, 29, 27, 28, 28, 35, 34, 32, 2…
$ afam     <chr> "no", "no", "no", "yes", "no", "no", "no", "no", "no", "no", …
$ hispanic <chr> "no", "no", "no", "no", "no", "no", "no", "no", "no", "no", "…
$ other    <chr> "no", "no", "no", "no", "no", "no", "no", "no", "no", "no", "…
$ work     <dbl> 0, 30, 0, 0, 22, 40, 0, 52, 0, 0, 0, 52, 52, 52, 8, 7, 0, 40,…
```


:::
:::


## Checking for missing data


::: {.cell}

```{.r .cell-code}
# Take a random sample of 1000 rows for visualization
fertility_sample <- fertility %>% 
  dplyr::slice_sample(n = 1000)

# Now visualize missingness on the sample
visdat::vis_miss(fertility_sample) +
  ggtitle(label = "Missing Data in Fertility Dataset (Sampled)") +
  theme(plot.title = element_text(size = 20, hjust = 0.5))
```

::: {.cell-output-display}
![](fertility_files/figure-html/unnamed-chunk-4-1.png){width=672}
:::

```{.r .cell-code}
visdat::vis_dat(fertility_sample, sort_type = TRUE, palette = "cb_safe") +
  labs(
    title = "Missing Data in Fertility Dataset (Sampled)",
    subtitle = "Showing Variable Types"
  ) +
  theme(
    plot.title = element_text(size = 20, hjust = 0.5),
    plot.subtitle = element_text(size = 18, hjust = 0.5)
  )
```

::: {.cell-output-display}
![](fertility_files/figure-html/unnamed-chunk-4-2.png){width=672}
:::
:::


## Dropping NA


::: {.cell}

```{.r .cell-code}
tidyr::drop_na(fertility_sample)
```

::: {.cell-output .cell-output-stdout}

```
# A tibble: 1,000 × 9
   rownames morekids gender1 gender2   age afam  hispanic other  work
      <dbl> <chr>    <chr>   <chr>   <dbl> <chr> <chr>    <chr> <dbl>
 1    18626 no       female  male       26 no    no       no       26
 2    56588 no       male    female     28 no    no       no       24
 3    54486 no       male    female     33 no    no       no        0
 4    97231 no       female  male       33 no    no       no       24
 5   246076 no       female  male       26 no    no       no        0
 6   144960 no       female  male       25 no    no       no       40
 7   222546 yes      female  female     33 no    no       no        0
 8    70657 no       female  male       33 no    no       no        0
 9    18525 no       male    female     35 no    no       no        0
10   123288 yes      female  female     32 no    no       no       52
# ℹ 990 more rows
```


:::
:::


## Cleaning Names


::: {.cell}

```{.r .cell-code}
fertility1 <- fertility %>%
  janitor::clean_names(case = "snake")  # clean column names

fertility1
```

::: {.cell-output .cell-output-stdout}

```
# A tibble: 254,654 × 9
   rownames morekids gender1 gender2   age afam  hispanic other  work
      <dbl> <chr>    <chr>   <chr>   <dbl> <chr> <chr>    <chr> <dbl>
 1        1 no       male    female     27 no    no       no        0
 2        2 no       female  male       30 no    no       no       30
 3        3 no       male    female     27 no    no       no        0
 4        4 no       male    female     35 yes   no       no        0
 5        5 no       female  female     30 no    no       no       22
 6        6 no       male    female     26 no    no       no       40
 7        7 no       female  male       29 no    no       no        0
 8        8 no       male    male       33 no    no       no       52
 9        9 no       female  male       29 no    no       no        0
10       10 no       male    female     27 no    no       no        0
# ℹ 254,644 more rows
```


:::
:::


##Getting names


::: {.cell}

```{.r .cell-code}
names(fertility_sample)
```

::: {.cell-output .cell-output-stdout}

```
[1] "rownames" "morekids" "gender1"  "gender2"  "age"      "afam"     "hispanic"
[8] "other"    "work"    
```


:::
:::


## TinyTable for fertility


::: {.cell}

```{.r .cell-code}
fertility_sample %>%
  head(10) %>%
  tinytable::tt(caption = "Fertility Dataset (Clean)") %>%
  tinytable::theme_html(class = "table table-hover table-striped table-condensed") %>%
  stats::setNames(c(
    "Row ID", 
    "More Kids", 
    "Gender 1", 
    "Gender 2", 
    "Age", 
    "African American", 
    "Hispanic", 
    "Other", 
    "Work"
  ))
```

::: {.cell-output-display}

```{=html}
<!-- preamble start -->

    <script>

      function styleCell_27683h6ri5w4rb9rx0rt(i, j, css_id) {
          var table = document.getElementById("tinytable_27683h6ri5w4rb9rx0rt");
          var cell = table.querySelector(`[data-row="${i}"][data-col="${j}"]`);
          if (cell) {
              console.log(`Styling cell at (${i}, ${j}) with class ${css_id}`);
              cell.classList.add(css_id);
          } else {
              console.warn(`Cell at (${i}, ${j}) not found.`);
          }
      }
      function spanCell_27683h6ri5w4rb9rx0rt(i, j, rowspan, colspan) {
        var table = document.getElementById("tinytable_27683h6ri5w4rb9rx0rt");
        const targetCell = table.querySelector(`[data-row="${i}"][data-col="${j}"]`);
        if (!targetCell) {
          console.warn(`Cell at (${i}, ${j}) not found.`);
        }

        // Get all cells that need to be removed
        const cellsToRemove = [];
        for (let r = 0; r < rowspan; r++) {
          for (let c = 0; c < colspan; c++) {
            if (r === 0 && c === 0) continue; // Skip the target cell
            const cell = table.querySelector(`[data-row="${i + r}"][data-col="${j + c}"]`);
            if (cell) {
              cellsToRemove.push(cell);
            }
          }
        }

        // Remove all cells
        cellsToRemove.forEach(cell => cell.remove());

        // Set rowspan and colspan of the target cell if it exists
        if (targetCell) {
          targetCell.rowSpan = rowspan;
          targetCell.colSpan = colspan;
        }
      }
      // tinytable span after
      window.addEventListener('load', function () {
          var cellsToStyle = [
            // tinytable style arrays after
          { positions: [ { i: '10', j: 0 }, { i: '10', j: 1 }, { i: '10', j: 2 }, { i: '10', j: 3 }, { i: '10', j: 4 }, { i: '10', j: 5 }, { i: '10', j: 6 }, { i: '10', j: 7 }, { i: '10', j: 8 },  ], css_id: 'tinytable_css_big4ho6bct1m37xg36rz',}, 
          { positions: [ { i: '0', j: 0 }, { i: '0', j: 1 }, { i: '0', j: 2 }, { i: '0', j: 3 }, { i: '0', j: 4 }, { i: '0', j: 5 }, { i: '0', j: 6 }, { i: '0', j: 7 }, { i: '0', j: 8 },  ], css_id: 'tinytable_css_rix9zx91s2dipdk3uxa7',}, 
          ];

          // Loop over the arrays to style the cells
          cellsToStyle.forEach(function (group) {
              group.positions.forEach(function (cell) {
                  styleCell_27683h6ri5w4rb9rx0rt(cell.i, cell.j, group.css_id);
              });
          });
      });
    </script>

    <style>
      /* tinytable css entries after */
      .table td.tinytable_css_big4ho6bct1m37xg36rz, .table th.tinytable_css_big4ho6bct1m37xg36rz { border-bottom: solid #d3d8dc 0.1em; }
      .table td.tinytable_css_rix9zx91s2dipdk3uxa7, .table th.tinytable_css_rix9zx91s2dipdk3uxa7 { border-top: solid #d3d8dc 0.1em; border-bottom: solid #d3d8dc 0.05em; }
    </style>
    <div class="container">
      <table class="table table-hover table-striped table-condensed" id="tinytable_27683h6ri5w4rb9rx0rt" style="width: auto; margin-left: auto; margin-right: auto;" data-quarto-disable-processing='true'>
        <thead>
        <caption>Fertility Dataset (Clean)</caption>
              <tr>
                <th scope="col" data-row="0" data-col="0">Row ID</th>
                <th scope="col" data-row="0" data-col="1">More Kids</th>
                <th scope="col" data-row="0" data-col="2">Gender 1</th>
                <th scope="col" data-row="0" data-col="3">Gender 2</th>
                <th scope="col" data-row="0" data-col="4">Age</th>
                <th scope="col" data-row="0" data-col="5">African American</th>
                <th scope="col" data-row="0" data-col="6">Hispanic</th>
                <th scope="col" data-row="0" data-col="7">Other</th>
                <th scope="col" data-row="0" data-col="8">Work</th>
              </tr>
        </thead>
        
        <tbody>
                <tr>
                  <td data-row="1" data-col="0">18626</td>
                  <td data-row="1" data-col="1">no</td>
                  <td data-row="1" data-col="2">female</td>
                  <td data-row="1" data-col="3">male</td>
                  <td data-row="1" data-col="4">26</td>
                  <td data-row="1" data-col="5">no</td>
                  <td data-row="1" data-col="6">no</td>
                  <td data-row="1" data-col="7">no</td>
                  <td data-row="1" data-col="8">26</td>
                </tr>
                <tr>
                  <td data-row="2" data-col="0">56588</td>
                  <td data-row="2" data-col="1">no</td>
                  <td data-row="2" data-col="2">male</td>
                  <td data-row="2" data-col="3">female</td>
                  <td data-row="2" data-col="4">28</td>
                  <td data-row="2" data-col="5">no</td>
                  <td data-row="2" data-col="6">no</td>
                  <td data-row="2" data-col="7">no</td>
                  <td data-row="2" data-col="8">24</td>
                </tr>
                <tr>
                  <td data-row="3" data-col="0">54486</td>
                  <td data-row="3" data-col="1">no</td>
                  <td data-row="3" data-col="2">male</td>
                  <td data-row="3" data-col="3">female</td>
                  <td data-row="3" data-col="4">33</td>
                  <td data-row="3" data-col="5">no</td>
                  <td data-row="3" data-col="6">no</td>
                  <td data-row="3" data-col="7">no</td>
                  <td data-row="3" data-col="8">0</td>
                </tr>
                <tr>
                  <td data-row="4" data-col="0">97231</td>
                  <td data-row="4" data-col="1">no</td>
                  <td data-row="4" data-col="2">female</td>
                  <td data-row="4" data-col="3">male</td>
                  <td data-row="4" data-col="4">33</td>
                  <td data-row="4" data-col="5">no</td>
                  <td data-row="4" data-col="6">no</td>
                  <td data-row="4" data-col="7">no</td>
                  <td data-row="4" data-col="8">24</td>
                </tr>
                <tr>
                  <td data-row="5" data-col="0">246076</td>
                  <td data-row="5" data-col="1">no</td>
                  <td data-row="5" data-col="2">female</td>
                  <td data-row="5" data-col="3">male</td>
                  <td data-row="5" data-col="4">26</td>
                  <td data-row="5" data-col="5">no</td>
                  <td data-row="5" data-col="6">no</td>
                  <td data-row="5" data-col="7">no</td>
                  <td data-row="5" data-col="8">0</td>
                </tr>
                <tr>
                  <td data-row="6" data-col="0">144960</td>
                  <td data-row="6" data-col="1">no</td>
                  <td data-row="6" data-col="2">female</td>
                  <td data-row="6" data-col="3">male</td>
                  <td data-row="6" data-col="4">25</td>
                  <td data-row="6" data-col="5">no</td>
                  <td data-row="6" data-col="6">no</td>
                  <td data-row="6" data-col="7">no</td>
                  <td data-row="6" data-col="8">40</td>
                </tr>
                <tr>
                  <td data-row="7" data-col="0">222546</td>
                  <td data-row="7" data-col="1">yes</td>
                  <td data-row="7" data-col="2">female</td>
                  <td data-row="7" data-col="3">female</td>
                  <td data-row="7" data-col="4">33</td>
                  <td data-row="7" data-col="5">no</td>
                  <td data-row="7" data-col="6">no</td>
                  <td data-row="7" data-col="7">no</td>
                  <td data-row="7" data-col="8">0</td>
                </tr>
                <tr>
                  <td data-row="8" data-col="0">70657</td>
                  <td data-row="8" data-col="1">no</td>
                  <td data-row="8" data-col="2">female</td>
                  <td data-row="8" data-col="3">male</td>
                  <td data-row="8" data-col="4">33</td>
                  <td data-row="8" data-col="5">no</td>
                  <td data-row="8" data-col="6">no</td>
                  <td data-row="8" data-col="7">no</td>
                  <td data-row="8" data-col="8">0</td>
                </tr>
                <tr>
                  <td data-row="9" data-col="0">18525</td>
                  <td data-row="9" data-col="1">no</td>
                  <td data-row="9" data-col="2">male</td>
                  <td data-row="9" data-col="3">female</td>
                  <td data-row="9" data-col="4">35</td>
                  <td data-row="9" data-col="5">no</td>
                  <td data-row="9" data-col="6">no</td>
                  <td data-row="9" data-col="7">no</td>
                  <td data-row="9" data-col="8">0</td>
                </tr>
                <tr>
                  <td data-row="10" data-col="0">123288</td>
                  <td data-row="10" data-col="1">yes</td>
                  <td data-row="10" data-col="2">female</td>
                  <td data-row="10" data-col="3">female</td>
                  <td data-row="10" data-col="4">32</td>
                  <td data-row="10" data-col="5">no</td>
                  <td data-row="10" data-col="6">no</td>
                  <td data-row="10" data-col="7">no</td>
                  <td data-row="10" data-col="8">52</td>
                </tr>
        </tbody>
      </table>
    </div>
<!-- hack to avoid NA insertion in last line -->
```

:::
:::


## Gender by work status


::: {.cell}

```{.r .cell-code}
fertility_sample %>%
  gf_bar(~gender1, fill = ~work, position = "dodge") %>%
  gf_labs(title = "Plot 3A: Dodged Bar Chart of Gender by Work Status") %>%
  gf_theme(theme(axis.text.x = element_text(
    size = 6,
    angle = 45, hjust = 0.5
  ))) %>%
  gf_refine(scale_fill_brewer(palette = "cb_safe"))
```

::: {.cell-output .cell-output-stderr}

```
Warning: Unknown palette: "cb_safe"
```


:::

::: {.cell-output .cell-output-stderr}

```
Warning: The following aesthetics were dropped during statistical transformation: fill.
ℹ This can happen when ggplot fails to infer the correct grouping structure in
  the data.
ℹ Did you forget to specify a `group` aesthetic or to convert a numerical
  variable into a factor?
```


:::

::: {.cell-output-display}
![](fertility_files/figure-html/unnamed-chunk-9-1.png){width=672}
:::
:::



::: {.cell}

```{.r .cell-code}
fertility <- read_csv("data/Fertility.csv")
```

::: {.cell-output .cell-output-stderr}

```
Rows: 254654 Columns: 9
── Column specification ────────────────────────────────────────────────────────
Delimiter: ","
chr (6): morekids, gender1, gender2, afam, hispanic, other
dbl (3): rownames, age, work

ℹ Use `spec()` to retrieve the full column specification for this data.
ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```


:::
:::


##  Count of Kids by Age Group


::: {.cell}

```{.r .cell-code}
fertility_sample %>%
  # Bin ages into groups of 10 years
  mutate(age_bin = cut(age,
                       breaks = seq(15, 55, by = 10),
                       labels = c("15–24", "25–34", "35–44", "45–54"),
                       right = FALSE)) %>%
  gf_bar(~age_bin, fill = ~morekids) %>%
  gf_labs(title = "Plot 4A: Counts of More Kids by Age ") %>%
  gf_refine(scale_fill_brewer(palette = "Set6"))
```

::: {.cell-output .cell-output-stderr}

```
Warning: Unknown palette: "Set6"
```


:::

::: {.cell-output-display}
![](fertility_files/figure-html/unnamed-chunk-11-1.png){width=672}
:::
:::


## Bar Chart of More Kids by Age Group


::: {.cell}

```{.r .cell-code}
fertility_sample %>%
  # Create age bins
  mutate(age_bin = cut(age,
                       breaks = seq(15, 55, by = 10),
                       labels = c("15–24", "25–34", "35–44", "45–54"),
                       right = FALSE)) %>%
  gf_bar(~age_bin,
         fill = ~morekids,
         position = "dodge") %>%
  gf_labs(title = "Plot 2A: Dodged Bar Chart of More Kids by Age Group") %>%
  gf_refine(scale_fill_brewer(palette = "Set2"))
```

::: {.cell-output-display}
![](fertility_files/figure-html/unnamed-chunk-12-1.png){width=672}
:::
:::


## Distribution of Age in Fertility Dataset


::: {.cell}

```{.r .cell-code}
# Age distribution
gf_histogram(~age, data = fertility_sample, bins = 20, fill = "coral") %>%
  gf_labs(title = "Distribution of Age in Fertility Dataset", x = "Age", y = "Count")
```

::: {.cell-output-display}
![](fertility_files/figure-html/unnamed-chunk-13-1.png){width=672}
:::
:::


## Count by work status


::: {.cell}

```{.r .cell-code}
# More Kids
gf_bar(~morekids, data = fertility_sample, fill = ~morekids) %>%
  gf_labs(title = "Counts by More Kids", x = "More Kids", y = "Count")
```

::: {.cell-output-display}
![](fertility_files/figure-html/unnamed-chunk-14-1.png){width=672}
:::

```{.r .cell-code}
# Gender1
gf_bar(~gender1, data = fertility_sample, fill = ~gender1) %>%
  gf_labs(title = "Counts by Gender1", x = "Gender 1", y = "Count")
```

::: {.cell-output-display}
![](fertility_files/figure-html/unnamed-chunk-14-2.png){width=672}
:::

```{.r .cell-code}
# Work status
gf_bar(~work, data = fertility_sample, fill = ~work) %>%
  gf_labs(title = "Counts by Work Status", x = "Work", y = "Count")
```

::: {.cell-output .cell-output-stderr}

```
Warning: The following aesthetics were dropped during statistical transformation: fill.
ℹ This can happen when ggplot fails to infer the correct grouping structure in
  the data.
ℹ Did you forget to specify a `group` aesthetic or to convert a numerical
  variable into a factor?
```


:::

::: {.cell-output-display}
![](fertility_files/figure-html/unnamed-chunk-14-3.png){width=672}
:::
:::


## Age by work status


::: {.cell}

```{.r .cell-code}
# Age by More Kids
gf_boxplot(morekids ~ age, data = fertility_sample, fill = ~morekids, orientation = "y") %>%
  gf_labs(title = "Age by More Kids", x = "Age", y = "More Kids")
```

::: {.cell-output-display}
![](fertility_files/figure-html/unnamed-chunk-15-1.png){width=672}
:::

```{.r .cell-code}
# Age by Work
gf_boxplot(work ~ age, data = fertility_sample, fill = ~work, orientation = "y") %>%
  gf_labs(title = "Age by Work Status", x = "Age", y = "Work")
```

::: {.cell-output .cell-output-stderr}

```
Warning: Continuous y aesthetic
ℹ did you forget `aes(group = ...)`?
```


:::

::: {.cell-output .cell-output-stderr}

```
Warning: The following aesthetics were dropped during statistical transformation: fill.
ℹ This can happen when ggplot fails to infer the correct grouping structure in
  the data.
ℹ Did you forget to specify a `group` aesthetic or to convert a numerical
  variable into a factor?
```


:::

::: {.cell-output-display}
![](fertility_files/figure-html/unnamed-chunk-15-2.png){width=672}
:::
:::


## Data Dictionary


::: {.cell}

```{.r .cell-code}
dplyr::count(fertility_sample)
```

::: {.cell-output .cell-output-stdout}

```
# A tibble: 1 × 1
      n
  <int>
1  1000
```


:::

```{.r .cell-code}
dplyr::summarise(fertility_sample)
```

::: {.cell-output .cell-output-stdout}

```
# A tibble: 1 × 0
```


:::

```{.r .cell-code}
crosstable::crosstable(fertility_sample)
```

::: {.cell-output .cell-output-stdout}

```
# A tibble: 24 × 4
   .id      label    variable   value                    
   <chr>    <chr>    <chr>      <chr>                    
 1 rownames rownames Min / Max  45.0 / 2.5e+05           
 2 rownames rownames Med [IQR]  1.3e+05 [6.3e+04;1.9e+05]
 3 rownames rownames Mean (std) 1.3e+05 (7.4e+04)        
 4 rownames rownames N (NA)     1000 (0)                 
 5 morekids morekids no         603 (60.30%)             
 6 morekids morekids yes        397 (39.70%)             
 7 gender1  gender1  female     483 (48.30%)             
 8 gender1  gender1  male       517 (51.70%)             
 9 gender2  gender2  female     503 (50.30%)             
10 gender2  gender2  male       497 (49.70%)             
# ℹ 14 more rows
```


:::
:::


