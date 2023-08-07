# Lecture and exercises on simulations in population genetics at Copenhagen University 2023

*These lecture materials were originally part of the ["Summer course in analysis of high throughput data for population genetics 2023"](http://popgen.dk/popgen23/).*

------------------------------------------------------------------------

### You can find the slides [here](https://bodkan.quarto.pub/ku-popgen2023/) (lecture, exercises, bonus content).

### [Here](https://bodkan.quarto.pub/ku-popgen2023-onepage/) is a one-page version of the slides (easier for quick reading).

------------------------------------------------------------------------

This README summarizes steps needed to set up your machine for the lecture and exercises. After you're done installing everything, make sure to run a small testing simulation (code below) to know that everything works as needed.

**For course participants:** If you don't want to work on your own laptop, you can also use the [online RStudio server](http://emily.popgen.dk:3838/) provided by the course organizers.

------------------------------------------------------------------------

# Installation instructions

In case you will be using RStudio (highly recommended), you **must** do [this](#workaround-for-an-rstudio-bug) first.

## If you want to use the online RStudio server

**If you want to use** the [online RStudio server](http://emily.popgen.dk:3838/) provided by the course organizers, the only thing you should do after logging into the RStudio session, is to run this bit of code:

```
library(slendr)
setup_env(agree = TRUE)
```

This will automatically install and set up necessary Python modules. **The server is quite slow, so the process can easily take five or more minutes!**

## If you want to use your own computer

You will need:

-   A working [R installation](https://cloud.r-project.org) (preferably version 4.x)
-   [RStudio](https://www.rstudio.com/products/rstudio/download/) (not crucial but highly recommended)

### *slendr* simulation package

Getting *slendr* to work is critical. The whole lecture is dedicated to this package.

First, run this in your R console:

```         
install.packages("slendr")
```

Assuming the above runs successfully, you will next need to setup a dedicated Python environment with tools we'll be using for simulation and analysis. To do this, *slendr* provides a helper function [`setup_env()`](https://www.slendr.net/reference/setup_env.html) that takes care of everything on your behalf.

First, load *slendr* itself.

```         
library(slendr)
```

This will very likely write a message that you are:

1.  missing SLiM -- this is OK, feel free to ignore this

2.  missing a Python environment.

Next, run the following command. This will ask for permission to install an isolated Python mini-environment just for *slendr* -- this won't affect your own Python setup at all, so don't be afraid to confirm this!

```         
setup_env(agree = TRUE)
```

Finally, make sure you get a positive confirmation from the following check:

```         
check_env()
```

## Other R package dependencies

I will use some tidyverse packages for analysis and plotting. You can install them with:

```         
install.packages(c("dplyr", "ggplot2"))"
```

# Testing the setup

Copy the following script to your R session after you successfully installed your R dependencies as described above.

```         
library(slendr)
init_env()

o <- population("outgroup", time = 1, N = 100)
b <- population("b", parent = o, time = 500, N = 100)
c <- population("c", parent = b, time = 1000, N = 100)
x1 <- population("x1", parent = c, time = 2000, N = 100)
x2 <- population("x2", parent = c, time = 2000, N = 100)
a <- population("a", parent = b, time = 1500, N = 100)

gf <- gene_flow(from = b, to = x1, start = 2100, end = 2150, rate = 0.1)

model <- compile_model(
  populations = list(a, b, x1, x2, c, o), gene_flow = gf,
  generation_time = 1, simulation_length = 2200
)

ts <- msprime(model, sequence_length = 10e6, recombination_rate = 1e-8)

ts
```

If this runs without error and you get a small summary table from the `ts` object, you're all set!

# Workaround for an RStudio bug {#workaround-for-an-rstudio-bug}

RStudio sometimes interferes with Python which is needed for simulations.

Go to \`Tools\` → \`Global Options\` in your RStudio and set the following options like this:

![](images/rstudio_setting.png)
